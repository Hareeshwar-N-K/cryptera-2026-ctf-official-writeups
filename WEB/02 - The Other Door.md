# The Other Door — Writeup

**Category:** Web  
**Difficulty:** Medium  
**Flag:** `CQCTF{dev_portal_on_•••••••••••••}`

---

## Challenge Description

> "The public website seems perfectly secure. But developers often leave backdoors for themselves. Check the site carefully — sometimes the map to the treasure is written on the back of the map itself."
>
> Link: `http://<IP>:8080`

---

## Solution

### Step 1: Explore the Main Website

Visit the provided URL on port 8080. You're greeted by **AstraMetrics**, a polished analytics dashboard website. The site has multiple pages (Home, About, Insights, Contact) and various interactive elements including a "Download Q4 Report" button (which always fails).

Nothing on the surface reveals the flag — the entire public site is a **decoy**.

### Step 2: Inspect the Source Code

The challenge hint says to "check the site carefully." Examining the page source or JavaScript files reveals a crucial comment.

Open **Developer Tools** (F12) → **Sources** tab, or view page source. Look through the JavaScript files. In `assets/js/main.js`, buried among legitimate-looking code, there's a developer comment:

```javascript
// Dev note: Internal testing portal running on http://localhost:9090 for pre-release feature validation
```

This reveals that there's a **second service running on port 9090**.

### Step 3: Alternative Discovery — Port Scanning

If you missed the source code hint, you could also discover the hidden service by running a port scan:

```bash
nmap -sV <target-IP>
```

This would reveal two open HTTP services:
- Port **8080**: The public AstraMetrics site
- Port **9090**: An unknown HTTP service

### Step 4: Visit the Hidden Portal

Navigate to `http://<IP>:9090`. You're presented with a terminal-styled **Dev Portal** page with animated output showing:

```
root@securecorp-dev:~$ whoami
dev-admin

root@securecorp-dev:~$ retrieve_flag --challenge=web_ctf_01
✓ Challenge completed successfully!
```

The flag is displayed on the page:

```
CQCTF{dev_portal_on_•••••••••••••}
```

---

## Infrastructure

The challenge runs two Docker containers via `docker-compose.yaml`:

```yaml
services:
  front_door:    # Public site on port 8080
    image: python:3.9-slim
    command: python -m http.server 8080
    volumes:
      - ./main-site:/app

  back_door:     # Hidden dev portal on port 9090
    image: python:3.9-slim
    command: python -m http.server 9090
    volumes:
      - ./dev-portal:/app
```

---

## Key Concepts

- **Service enumeration**: Real-world web applications often run multiple services on different ports. Always scan for additional open ports.
- **Source code review**: Developer comments, debug endpoints, and internal URLs are frequently left in production code. Always inspect JavaScript files and HTML source.
- **Hidden services / Shadow IT**: Development portals, admin panels, and debug interfaces left exposed are a common attack surface in penetration testing.

---

## Tools Used

- Browser Developer Tools (F12) — source code inspection
- nmap (optional — port scanning)
- Web browser
