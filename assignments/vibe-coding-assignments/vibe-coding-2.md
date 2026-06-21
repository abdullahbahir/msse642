# Security Misconfiguration Lab — Vibe Coding Assignment #2

**Course:** MSSE 642 – Software Assurance  
**Project:** OWASP Top 10:2025 — A02: Security Misconfiguration  
**Student:** Abdullah Bahir  
**Date:** June 20, 2026  
**Live Demo:** [Insert your Replit app URL here]

---

## Overview: Vibe Coding Tool

I chose **Replit Agent** as my vibe coding tool. Replit Agent is an AI-powered development assistant built directly into the Replit IDE that generates, edits, and debugs full-stack code through natural language prompts without requiring manual project setup.

I chose it because it handled the entire project structure in one place: it scaffolded the React frontend, created the shared UI components, and wired the six demo pages together in a single development environment. This let me focus on the security concepts rather than boilerplate. Iteration was fast — I could describe a change such as “add a directory listing demo with clickable files” and it would produce working interactive components immediately.

---

## Description of the Program

I built the **Security Misconfiguration Lab** — an interactive, hands-on educational tool that lets users explore six distinct misconfiguration vulnerabilities side by side in vulnerable and secure modes rather than just reading about them.

The application is entirely frontend-based. All demos are simulated in the browser, which means there are no real credentials, no real databases, and no risk of accidental data exposure. Each lab includes a central **Vulnerable / Secure** toggle that instantly switches between the two states so users can compare insecure and hardened configurations in the same interface.

### The Six Labs

| Lab | What it demonstrates |
|---|---|
| Default Credentials | Factory-default usernames and passwords granting immediate admin access |
| Verbose Errors | Stack traces leaking server paths, database credentials, and version information |
| Security Headers | Missing HTTP headers leaving users open to XSS, clickjacking, MIME sniffing, and SSL stripping |
| Directory Listing | Web server exposing internal files such as `.env`, private keys, and SQL backups |
| Cloud Storage | Publicly accessible S3-style bucket exposing customer data and API keys |
| Debug Mode | Production app running with `DEBUG=True`, dumping environment details on any error |

### Lab 1 — Default Credentials

The demo simulates a fictional **AdminPanel v1.0** login screen. In Vulnerable Mode, preset attack buttons fill the form with well-known default credentials such as `admin/admin`, `root/root`, and `admin/123456`. Submitting them grants access and displays a fake admin dashboard with user counts, API keys, and a partial secret key.

In Secure Mode, all default-credential attempts are rejected with a message confirming that common passwords are not accepted.

### Lab 2 — Verbose Errors

The demo simulates a `/api/users/:id` endpoint. Entering an injection-style payload such as `abc'--` triggers an error.

In Vulnerable Mode, the response is a raw stack trace containing:

- The exact SQL query that failed
- Internal file paths such as `/var/www/app/models/User.php`
- Server software version details such as PHP and Apache versions
- Database hostnames and plaintext credentials

In Secure Mode, the response is a single generic JSON object with no internal detail.

### Lab 3 — Security Headers

The demo renders an HTTP response inspector. In Vulnerable Mode, the response is missing the six key security headers. Each missing header is marked with a red indicator.

Clicking any row expands a panel explaining:

- What the header controls
- What attack it helps prevent, such as XSS, clickjacking, MIME sniffing, or SSL stripping

In Secure Mode, every header is present with its recommended value, and each row turns green.

### Lab 4 — Directory Listing

The demo renders a fake Apache-style file browser. In Vulnerable Mode, the listing includes sensitive files such as:

| File | Risk |
|---|---|
| `.env` | Database host, credentials, AWS secret, SMTP password |
| `config.php` | Hardcoded root credentials and AWS keys |
| `backup.sql` | Full production database dump |
| `private-keys.pem` | RSA private key |
| `install.php` | Setup script with default admin credentials |

Clicking any file shows its fake contents.

In Secure Mode, the server returns a `403 Forbidden` response and directory browsing is completely blocked.

### Lab 5 — Cloud Storage

The demo simulates a public S3-style object storage bucket. In Vulnerable Mode, a bucket listing shows sensitive files including `customer-data.csv`, `api-keys.txt`, and `config/production.env`. Clicking **Get** on any sensitive file triggers a simulated download and previews the exposed content, including fake Social Security numbers, credit card numbers, and API tokens.

In Secure Mode, the bucket returns `403 Forbidden` and `AllPublicAccessBlocked`.

### Lab 6 — Debug Mode

The demo simulates a Django-powered web application. Clicking **Trigger Error** sends a bad database query.

In Vulnerable Mode, Django’s debug page returns the full error context:

- The exact SQL query
- Local variable values at the time of the crash
- `settings.DATABASE_URL` with plaintext credentials
- `settings.SECRET_KEY` exposed in plaintext
- Server hostname and Python/Django version

In Secure Mode, the app returns a plain `500` page with a reference code only — the behavior expected in production.

---

## Description of the Vulnerability — OWASP A02:2025 Security Misconfiguration

Security Misconfiguration occurs when security settings are missing, left at insecure defaults, applied inconsistently, or not maintained over time.

It is one of the most common and dangerous categories in the OWASP Top 10 because it does not require a code flaw. A perfectly written application can still be exposed if the deployment, server, cloud service, or framework is configured incorrectly.

Common causes include:

- Not defining secure defaults, such as missing headers or open access controls
- Leaving default passwords or debug settings enabled
- Promoting development settings into production
- Failing to remove unnecessary services, ports, or features

### Common Subcategories

#### Default Credentials
Routers, cameras, databases, CMS platforms, and cloud dashboards often ship with factory passwords. If those passwords are never changed, attackers can gain access immediately.

#### Verbose Error Messages
Detailed error output is helpful during development but dangerous in production. A single uncaught exception can reveal file paths, version numbers, database hostnames, and even secrets that help an attacker continue the attack.

#### Missing Security Headers
Browsers enforce security policies only when servers send the correct headers. Without them, the browser defaults to permissive behavior and becomes easier to abuse.

#### Directory Listing
If a web server is configured to browse directories when no default file exists, users may see backups, configuration files, or private keys that should never be public.

#### Cloud Storage Misconfiguration
Cloud buckets and object storage services can be accidentally left public. When that happens, anyone who finds the bucket can list or download sensitive files.

#### Debug Mode in Production
Framework debug pages often expose environment variables, stack traces, and application secrets. Those features are useful locally but should never be exposed to the public internet.

---

## Recent Real-World Incidents

| Year | Incident | Impact |
|---|---|---|
| 2023 | **Microsoft Power Platform / Power Apps portals** — Some portals were misconfigured to allow public table access by default. | Sensitive records from multiple organizations were exposed until the default behavior was corrected. |
| 2022 | **Toyota** — A cloud asset was left exposed with access to customer vehicle data. | Customer location and vehicle information remained exposed for an extended period before discovery. |
| 2021 | **Twitch** — A server misconfiguration exposed source code, creator payout data, and internal security tooling. | Large volumes of internal data were leaked publicly. |
| 2019 | **Capital One** — A misconfigured web application firewall enabled SSRF against the AWS metadata service and exposed over-permissive cloud access. | More than 100 million customer records were stolen, leading to a major regulatory settlement. |

---

## Problems Encountered and How I Solved Them

### Problem 1 — Missing Lab Content After the Initial Scaffold

After the AI agent generated the main layout and shared components, it ran out of context before finishing all six individual demo sections. The application felt incomplete because several lab screens were missing or only partially implemented.

**Solution:** I reviewed the generated structure, identified the missing sections, and completed each lab manually so the app had consistent vulnerable and secure views throughout.

### Problem 2 — The Secure and Vulnerable States Needed Clearer Separation

Early versions of the lab showed both states, but the differences were not obvious enough for a class demonstration.

**Solution:** I added stronger visual labels, clearer explanation text, and dedicated status banners so users could immediately understand whether a configuration was safe or unsafe.

### Problem 3 — Simulated Secrets Needed to Feel Real Without Exposing Real Data

The lab needed realistic examples of sensitive information such as API keys, database credentials, and environment variables, but it could not contain real secrets.

**Solution:** I used clearly fake sample values and structured them to resemble real production data closely enough for teaching purposes while keeping the project safe and non-sensitive.

---

## Key Takeaways

This project showed that security misconfiguration is often about what is missing or incorrectly enabled rather than about broken application logic. The lab makes that lesson visible by letting users compare insecure and secure configurations side by side.

The biggest takeaway is that hardening an application requires more than secure code. It also requires safe defaults, proper deployment settings, and ongoing configuration review across the entire environment.

