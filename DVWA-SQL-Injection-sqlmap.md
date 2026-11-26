# Automated SQL Injection & Credential Extraction (DVWA)

| | |
|---|---|
| **Target** | 192.168.56.107 (DVWA — Damn Vulnerable Web Application) |
| **Severity** | 🔴 Critical |
| **Tool** | sqlmap |
| **Status** | Confirmed — database dumped, credentials cracked |

[← Back to README](./README.md)

## Executive Summary

The `id` parameter on DVWA's SQL Injection page was vulnerable to SQL injection. A basic sqlmap scan against the live request failed to detect it, but escalating sqlmap's detection aggressiveness and adding a WAF/filter-evasion tamper script successfully confirmed and exploited the injection. The `users` table was fully enumerated and dumped, and the extracted password hashes were cracked back to plaintext using sqlmap's built-in dictionary attack.

## Technical Details

| Field | Value |
|-------|-------|
| **Vulnerability** | SQL Injection (CWE-89) |
| **Injection Point** | `id` GET parameter — `/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit` |
| **Back-end DBMS** | MySQL ≥ 4.1 |
| **Web Stack** | Apache 2.2.8, PHP 5.2.4, Ubuntu 8.04 (Hardy Heron) |
| **Exploit Tool** | sqlmap, run against a saved raw HTTP request (`-r`) |

## Methodology

1. **Session capture** — Intercepted an authenticated request to the SQLi page with Burp Suite and saved it as a raw HTTP request file, preserving the `PHPSESSID` cookie so sqlmap could reuse the logged-in session (`sqlmap -r <request_file>`).
2. **Initial scan (failed)** — A default sqlmap run against the `id` parameter reported `all tested parameters do not appear to be injectable`, despite testing boolean-based, error-based, UNION-based, and time-based techniques across MySQL, PostgreSQL, Oracle, and SQL Server syntax. sqlmap's own output suggested the target might be filtering input (possible WAF) and recommended increasing `--level`/`--risk` or applying a `--tamper` script.
3. **Escalated scan (successful)** — Re-ran sqlmap with `--level=5 --risk=3 --tamper=space2comment`. Raising the level/risk expands the technique and payload set sqlmap tries, and `space2comment` rewrites spaces in the payload as inline SQL comments (`/**/`) to slip past basic keyword/space-based filtering. This combination successfully confirmed the injection.
4. **Enumeration** — Used sqlmap to identify the back-end DBMS (MySQL ≥ 4.1) and enumerate the `dvwa` database's `users` table structure (columns, then rows).
5. **Data extraction** — Dumped the full `users` table (5 entries): `user_id`, `user`, `avatar`, `password` (MD5 hash), `last_name`, `first_name`.
6. **Credential cracking** — Used sqlmap's built-in dictionary-based hash cracking (`md5_generic_passwd`) against the extracted password hashes.

## Evidence

**Dumped `users` table (MD5 hash → cracked plaintext):**

| user_id | user | password hash | cracked password |
|---|---|---|---|
| 1 | admin | `5f4dcc3b5aa765d61d8327deb882cf99` | password |
| 2 | gordonb | `e99a18c428cb38d5f260853678922e03` | abc123 |
| 3 | 1337 | `8d3533d75ae2c3966d7e0d4fcc69216b` | charley |
| 4 | pablo | `0d107d09f5bbe40cade3de5c71e9e9b7` | letmein |
| 5 | smithy | `5f4dcc3b5aa765d61d8327deb882cf99` | password |

Output was also saved locally by sqlmap to `~/.local/share/sqlmap/output/192.168.56.107/dump/dvwa/users.csv`.

## Detection

- **Application-layer logs** reviewed for the DVWA/Apache access log would show a high volume of requests with abnormal SQL syntax in the `id` parameter in a short window (sqlmap sends large numbers of test payloads per technique), a strong signature for automated SQLi scanning.
- A WAF or IDS rule matching common SQLi tamper patterns (inline comments replacing whitespace, `UNION SELECT`, `SLEEP()`, `EXTRACTVALUE()`, stacked query indicators) would have flagged this traffic; the fact the basic scan failed but the tamper-based scan succeeded indicates some filtering was present but was not comprehensive.
- Database-side query logging/audit (if enabled) would show the injected queries directly, including the enumeration queries against `information_schema` that sqlmap uses to map table/column names.

## Remediation Recommendations

1. **Parameterized queries** — Use prepared statements/parameterized queries exclusively; never concatenate user input directly into SQL strings, which is the root cause enabling injection regardless of filtering.
2. **Input validation** — Apply strict server-side validation on the `id` parameter (expect a plain integer) rather than relying on keyword/pattern-based filtering, which `--tamper` techniques like `space2comment` are specifically designed to evade.
3. **Least privilege** — The database account used by the application should not have read access to other users' password hashes or unrelated tables; limit it to only what the application function requires.
4. **Password storage** — MD5 is cryptographically broken and crackable via dictionary/rainbow-table attacks, as demonstrated here. Passwords should be hashed with a modern, salted algorithm (bcrypt, scrypt, or Argon2).
5. **WAF tuning** — If a WAF is in place, ensure it normalizes/decodes input (strips comments, collapses whitespace substitutions) before pattern matching, rather than relying on raw keyword/space detection that a tamper script can bypass.

---

> This report documents activity performed in an isolated, personal lab environment (DVWA) for educational purposes only.
