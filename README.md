# DVWA – Cross-Site Scripting (XSS) Penetration Testing Lab

## 1. Project Overview

This project demonstrates Reflected and Stored Cross-Site Scripting (XSS) in Damn Vulnerable Web Application (DVWA v1.0.7), hosted on Metasploitable 2.

Testing was performed manually from a Windows 11 host machine using a web browser. The tests were conducted at DVWA **Low** security level in an isolated VMware lab.

## 2. Lab Environment

| Component | Details |
|---|---|
| Testing machine | Windows 11 host |
| Target | Metasploitable 2 VM |
| Web application | DVWA v1.0.7 |
| Testing method | Manual browser-based testing |
| Browser | Web browser |
| Security level | Low |
| Virtualization | VMware |

## 3. Objectives

- Understand Cross-Site Scripting.
- Test Reflected XSS.
- Test Stored XSS.
- Observe browser-side JavaScript execution using harmless alert payloads.
- Compare reflected and stored input handling.
- Document impact and remediation.

---

# 4. Practical Walkthrough

## Part A – Reflected Cross-Site Scripting

### Step 1 – Normal input test

Opened the **XSS (Reflected)** module and entered a normal name:

```text
kameel
```

**Observation:** The application displayed a greeting containing the submitted name. This established the normal behavior before testing.

**Evidence – Reflected XSS page with normal input**

![Reflected XSS normal input](Screenshots/02_XSS/01_Reflected_XSS_Normal_Input.png)

### Step 2 – Submit a harmless JavaScript test payload

**Payload**
```html
<script>alert('XSS Vulnerability')</script>
```

**Payload breakdown**

| Component | Meaning |
|---|---|
| `<script>` | Starts an HTML script element |
| `alert(...)` | Calls the JavaScript alert function |
| `'XSS Vulnerability'` | Text displayed in the alert dialog |
| `</script>` | Closes the script element |

**How it works:** The application reflects the supplied value in its response. If the value is inserted into an executable HTML context without context-appropriate output encoding, the browser parses the script element and executes its JavaScript.

**Evidence – Payload submitted**

![Reflected XSS payload submitted](Screenshots/02_XSS/02_Reflected_XSS_Payload_Submitted.png)

**Evidence – JavaScript alert displayed**

![Reflected XSS alert](Screenshots/02_XSS/03_Reflected_XSS_JavaScript_Alert.png)

**Result:** The browser displayed the alert text `XSS Vulnerability`, demonstrating JavaScript execution in the reflected response.

---

## Part B – Stored Cross-Site Scripting

### Step 1 – Open the Stored XSS guestbook

Opened the **XSS (Stored)** module. The page provided a guestbook form with Name and Message fields, along with previously submitted entries.

**Evidence – Stored XSS guestbook page**

![Stored XSS guestbook page](Screenshots/02_XSS/04_Stored_XSS_Guestbook_Page.png)

### Step 2 – Submit a normal guestbook entry

**Name**
```text
Kameel
```

**Message**
```text
This is my first XSS test
```

**Observation:** The application displayed the submitted entry in the guestbook.

**Evidence – Normal guestbook entry**

![Stored XSS normal entry](Screenshots/02_XSS/05_Stored_XSS_Normal_Entry.png)

### Step 3 – Submit a harmless JavaScript test payload

**Name**
```text
Kameel
```

**Message**
```html
<script>alert('Stored XSS')</script>
```

**Payload breakdown**

| Component | Meaning |
|---|---|
| `<script>` | Starts an HTML script element |
| `alert(...)` | Displays a browser alert dialog |
| `'Stored XSS'` | Text displayed in the alert |
| `</script>` | Closes the script element |

**How it works:** The guestbook stores the submitted message. When the application later renders that message without safely encoding it for the HTML context, the browser interprets the script element and executes it.

**Evidence – Stored XSS payload entered**

![Stored XSS payload entered](Screenshots/02_XSS/06_Stored_XSS_Payload_Entered.png)

**Evidence – JavaScript alert displayed**

![Stored XSS alert](Screenshots/02_XSS/07_Stored_XSS_JavaScript_Alert.png)

**Result:** The browser displayed `Stored XSS`, demonstrating that the guestbook content was rendered as executable script rather than inert text.

---

# 5. Reflected XSS vs Stored XSS

| Feature | Reflected XSS | Stored XSS |
|---|---|---|
| Input handling | Reflected in the response | Saved by the application |
| Persistence | Not stored by the vulnerable feature | Persists in stored content |
| Execution point | When the crafted response is rendered | When stored content is rendered |
| Lab example | Name input | Guestbook message |

# 6. Findings and Potential Impact

| Test | Finding | Potential impact |
|---|---|---|
| Reflected XSS | Harmless JavaScript alert executed from reflected input | Script execution in a user's browser |
| Stored XSS | Harmless JavaScript alert executed from guestbook content | Persistent script execution for users viewing the affected content |

These tests demonstrate unsafe handling of untrusted input in browser-rendered HTML. The alert payloads prove script execution; they do not by themselves demonstrate account or data compromise.

# 7. Remediation

1. **Context-aware output encoding:** Encode untrusted data before inserting it into HTML, attributes, JavaScript, CSS or URL contexts.
2. **Safe DOM APIs:** Prefer `textContent` and safe DOM construction over inserting untrusted strings with `innerHTML`.
3. **Input validation:** Validate expected formats and lengths. Input validation is supplementary and does not replace output encoding.
4. **Content Security Policy:** Deploy a restrictive CSP as an additional defense-in-depth control.
5. **Avoid unsafe HTML rendering:** Sanitize HTML with a maintained allow-list sanitizer if users genuinely need to submit formatted HTML.
6. **Cookie protections:** Set appropriate `HttpOnly`, `Secure` and `SameSite` cookie attributes. These reduce some risks but do not fix the XSS vulnerability itself.

# 8. Conclusion

Reflected XSS and Stored XSS were successfully demonstrated in DVWA v1.0.7 running on Metasploitable 2. A harmless JavaScript alert payload executed when reflected input and stored guestbook content were rendered by the browser.

The exercise provided practical experience in identifying unsafe output handling, understanding the difference between reflected and persistent XSS, collecting evidence and documenting remediation.

# 9. Disclaimer

This project was performed for educational purposes in an isolated, intentionally vulnerable virtual lab. Testing was limited to systems under the lab operator's control. Do not test systems without explicit authorization.
