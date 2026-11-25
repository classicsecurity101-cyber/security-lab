# Web Application Security: Server-Side Vulnerabilities (PortSwigger Web Security Academy)

| | |
|---|---|
| **Environment** | PortSwigger Web Security Academy |
| **Category** | Server-Side Vulnerabilities |
| **Status** | Labs completed — Path Traversal, Access Control, File Upload, OS Command Injection, SSRF |

[← Back to README](./README.md)

## Executive Summary

Completed a set of Web Security Academy labs covering five server-side vulnerability classes: path traversal, broken access control, unrestricted file upload, OS command injection, and server-side request forgery (SSRF). Each lab involved intercepting and modifying requests with Burp Suite to break an assumption the application made about user input, file handling, or network trust boundaries.

## 1. Path Traversal

| Field | Value |
|-------|-------|
| **Vulnerability** | Path Traversal (Directory Traversal) |
| **CWE** | CWE-22 — Improper Limitation of a Pathname to a Restricted Directory |

**Technique:** The application accepted a filename/path parameter to retrieve a file (e.g. an image) and passed it to the filesystem without properly restricting it to the intended directory.

**Payload:** Modified the path parameter to traverse upward out of the intended directory (e.g. `../../../../etc/passwd`), bypassing the directory restriction.

**Evidence:** The server returned the contents of `/etc/passwd`, confirming arbitrary file read on the host filesystem.

## 2. Access Control

| Field | Value |
|-------|-------|
| **Vulnerability** | Broken Access Control |
| **CWE** | CWE-862 / CWE-863 — Missing/Incorrect Authorization |

**Concepts covered:**
- **Horizontal privilege escalation** — accessing another user's data/account by manipulating an identifier (e.g. a user ID or account reference) the application trusted without re-checking ownership.
- **Vertical privilege escalation** — a lower-privileged user reaching functionality or data intended only for a higher-privileged role (e.g. an admin-only function) because the server failed to enforce role checks on that request.

**Evidence:** Successfully accessed another user's account/data by changing the relevant identifier in the request, confirming the server trusted client-supplied values instead of enforcing authorization per request.

## 3. File Upload Vulnerability

| Field | Value |
|-------|-------|
| **Vulnerability** | Unrestricted Upload of Dangerous File Type |
| **CWE** | CWE-434 — Unrestricted Upload of File with Dangerous Type |

**Technique:**
1. Uploaded a legitimate image as a profile picture to confirm the normal upload flow.
2. Attempted to directly upload a `.php` file disguised as an image — blocked by the application's upload filtering.
3. Bypassed the filter by intercepting the **GET** request that referenced the uploaded image's file path, and modifying the path so it pointed to the uploaded PHP file instead of the image.

**Evidence:** The server executed the PHP file and returned the lab's secret in the response, confirming server-side code execution via the upload functionality.

## 4. OS Command Injection

| Field | Value |
|-------|-------|
| **Vulnerability** | OS Command Injection |
| **CWE** | CWE-78 — Improper Neutralization of Special Elements used in an OS Command |

**Technique:** Identified an application feature that passed user-supplied input directly into a server-side shell command without sanitization.

**Payload:** Injected a shell metacharacter followed by the `whoami` command, appending it to the expected input so the underlying OS executed both the intended command and the injected one.

**Evidence:** The response included the output of `whoami`, confirming arbitrary OS command execution on the server and revealing the service account the web application runs as.

## 5. Server-Side Request Forgery (SSRF)

| Field | Value |
|-------|-------|
| **Vulnerability** | Server-Side Request Forgery |
| **CWE** | CWE-918 — Server-Side Request Forgery |

**Lab 1 — Basic SSRF against an internal admin interface**
- The application's "check stock" feature fetched a URL supplied by the client to query a stock-checking service.
- Changed the target URL to `http://localhost/admin`, causing the server itself to request its own internal admin interface — an endpoint not reachable directly from outside the network.
- Used the reflected access to the internal admin panel to delete a user account, confirming full SSRF-driven access to an internal-only interface.

**Lab 2 — SSRF with internal network/IP discovery**
- The internal admin panel's address wasn't known in advance, so the IP needed to be discovered first.
- Used **Burp Intruder** with a numeric payload set (1–255) against the last octet of the internal IP range, sending a request through the vulnerable fetch feature for each candidate address.
- Reviewed the response **status codes** across all 255 requests and identified the one returning `200 OK` as the live internal host.
- Used that discovered internal IP to reach the admin panel through the SSRF and complete the lab.

## CWE Summary

| Vulnerability | CWE |
|---|---|
| Path Traversal | CWE-22 |
| Broken Access Control | CWE-862 / CWE-863 |
| Unrestricted File Upload | CWE-434 |
| OS Command Injection | CWE-78 |
| Server-Side Request Forgery | CWE-918 |

## Remediation Recommendations

1. **Path Traversal** — Canonicalize and validate file paths server-side against an allow-list of permitted files/directories; never build filesystem paths directly from user input.
2. **Access Control** — Re-verify authorization on every request server-side (never trust a client-supplied ID or role claim); apply the principle of least privilege and deny-by-default for admin functionality.
3. **File Upload** — Validate file type server-side by content (not file extension or client-supplied MIME type), store uploads outside the webroot or in a location the web server won't execute, and strip/rename files on upload.
4. **OS Command Injection** — Avoid passing user input to shell commands entirely where possible; if unavoidable, use parameterized system APIs rather than string-built shell commands, and apply strict input allow-listing.
5. **SSRF** — Enforce an allow-list of permitted destination hosts/protocols for any server-side fetch feature, block requests to internal/private IP ranges (including `localhost` and cloud metadata endpoints) at the application layer, and apply network segmentation so the web server has no route to sensitive internal services it doesn't need.

---

> All activity in this report was performed against PortSwigger's Web Security Academy, an intentionally vulnerable platform provided for educational purposes.
