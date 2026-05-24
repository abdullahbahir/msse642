# Security Risk Analysis — Vibe Coding Assignment #1

**OWASP Vulnerability:** A07:2025 — Authentication Failures  
**Due:** Week 3

---

## Overview: Vibe Coding Tool

I chose **Replit Agent** as my vibe coding tool. Replit Agent is an AI-powered development assistant built directly into the Replit IDE that can generate, edit, and debug full-stack code through natural language prompts — no manual file setup or configuration required.

I chose it because it handles the entire stack in one place: it scaffolded the React frontend, Express API server, and shared TypeScript libraries simultaneously, wired them together with a reverse proxy, and set up the OpenAPI contract between them. This let me focus on the security concepts rather than boilerplate. It was also easy to iterate — I could describe a change like "add a new SQL injection attack type" and it would update both the backend logic and the frontend UI at once.

---

## Description of the Program

I built a **Secure Login Simulator** — an interactive, side-by-side educational tool that lets users experience authentication vulnerabilities hands-on rather than just reading about them.

The app has two login panels running against real API endpoints:

- **VULNERABLE ENDPOINT** — a login route that concatenates user input directly into a SQL query string and stores passwords as plain text
- **SECURE ENDPOINT** — a login route that uses parameterized queries, bcrypt password hashing (cost factor 10), and rate limiting (5 attempts per 60 seconds)

For each login attempt, the simulator displays the actual SQL query that ran, a detailed explanation of what the attack did or why it was blocked, and a live system log of all attempts colour-coded by outcome. A database table shows the difference between plain-text password storage and bcrypt-hashed storage side by side.

I chose this type of program because authentication is something every developer touches, yet it remains one of the most commonly misconfigured areas in real systems. A side-by-side comparison — where you run the exact same attack against both endpoints and see opposite outcomes — makes the vulnerability concrete in a way that documentation alone cannot.


---

## Description of the Vulnerability — OWASP A07:2025 Authentication Failures

**Authentication Failures** (formerly "Broken Authentication") occur when an application does not properly verify who a user is, allowing attackers to compromise passwords, keys, or session tokens — or exploit other implementation flaws to assume other users' identities.

The specific sub-vulnerabilities demonstrated in this app are:

### 1. SQL Injection Against the Login Form

When user input is concatenated directly into a SQL string instead of using parameterized queries, an attacker can inject SQL syntax that changes the meaning of the query. This simulator demonstrates four distinct techniques:

| Payload | Technique | Effect |
|---|---|---|
| `admin' AND 1=1--` | Boolean-Blind (true) | Forces a true condition so the database returns the user row without a valid password |
| `admin' AND 1=2--` | Boolean-Blind (false) | Forces a false condition, locking out even legitimate users — demonstrating attackers can control outcomes in both directions |
| `admin'; DROP TABLE users--` | Stacked Query / DDL | Terminates the login query and appends a second destructive statement, potentially deleting entire tables |
| `admin' AND (SELECT COUNT(*) FROM users)>0--` | Subquery Probe | Embeds a nested `SELECT` to fingerprint the database schema without any visible output |

### 2. Plain-Text Password Storage

The vulnerable endpoint stores passwords in clear text. If the database is ever leaked, every user's actual password is immediately exposed with no cracking required.

### 3. No Rate Limiting

The vulnerable endpoint allows unlimited login attempts, making it trivially susceptible to brute-force and credential-stuffing attacks.

### Recent Real-World Incidents

- **RockYou2024 (2024)** — Nearly 10 billion plain-text passwords were leaked in a compilation file published on a hacking forum, directly enabling credential-stuffing attacks against sites that store or reuse passwords insecurely.
- **23andMe (2023)** — Attackers used credential stuffing (trying leaked username/password pairs from other breaches) to compromise approximately 6.9 million user accounts. The company had no rate limiting or anomaly detection on login attempts.
- **Optus (2022, Australia)** — An unauthenticated API endpoint exposed customer records for 9.8 million Australians. No authentication was required at all to access production data — a direct Authentication Failure under OWASP A07.

---

## Problems Encountered and How I Solved Them

### Problem 1 — Attack results not displaying for failed logins

The frontend only handled the `onSuccess` callback of the login mutation, but SQL injection attacks that return HTTP 401 (auth failed) were treated as errors and silently dropped. The result card never appeared for blocked attacks.

**Solution:** I added an `onError` handler to the React Query mutation. The API always returns a structured JSON body even for 401 responses, so I accessed `error.data` (the typed error body from the generated API client) and passed it to the same result-display function as a successful response.

### Problem 2 — The teacher already demonstrated `OR '1'='1` and `UNION SELECT`

Using the same payloads as the teacher would make the project indistinguishable from the lecture examples.

**Solution:** I replaced both payloads with four distinct, less commonly demonstrated techniques: Boolean-Blind (true and false variants), Stacked Query with DDL, and Subquery-based schema probing. Each has its own detection branch in the backend with a tailored explanation of how it works.

### Problem 3 — The UI was too tall to screenshot in one frame

The original layout used generous padding and large font sizes, requiring scrolling to see both panels.

**Solution:** I reduced padding, input heights, font sizes, and gap values across every component so the entire application fits within a standard 1280×900 viewport with no scrolling needed.
