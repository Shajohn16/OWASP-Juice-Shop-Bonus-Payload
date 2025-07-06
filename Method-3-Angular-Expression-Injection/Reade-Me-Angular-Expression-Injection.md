# ⚔️ Method 3 – AngularJS Expression Injection via Product Review Field

---

### 🔍 Attack Vector: AngularJS Template Injection via Comment/Review Fields

This technique exploits the client-side templating behavior of AngularJS, wherein dynamic content rendered through `{{expression}}` syntax may be evaluated in unsafe contexts. When user input is directly embedded into AngularJS templates without sanitization or context escaping, malicious expressions can be evaluated and executed — leading to **DOM-based template injection**, also known as **AngularJS sandbox escape**.

---

### 🧠 Technical Breakdown: Why This Works

AngularJS evaluates expressions in `{{ ... }}` blocks using `$parse`, which, in unsafe contexts, allows access to JavaScript constructors and even arbitrary code execution. By abusing prototype chains and constructor access, we can craft payloads like:

```js
{{constructor.constructor('alert(1337)')()}}
This uses:

constructor: Accesses the constructor of the current scope.

constructor.constructor: Escalates to Function.

Function('alert(1337)')(): Executes arbitrary JS.

If Angular renders user-controlled input directly inside a binding context (e.g., product review or admin dashboard), this payload will be executed in the browser.

🔬 Payload Variants
Unencoded:

js
Copy
Edit
{{constructor.constructor('alert(1)')()}}
Encoded (for stealth or filter bypass):

js
Copy
Edit
%7B%7Bconstructor.constructor%28%27alert%281%29%27%29%28%29%7D%7D
Alternative sandbox escapes (older Angular versions):

js
Copy
Edit
{{[].pop.constructor('alert(1)')()}}
{{[].filter.constructor('alert(1)')()}}
🛠️ Execution Flow:
Attacker navigates to a product page or feedback form.

Enters the malicious AngularJS expression in the review/comment field.

Frontend renders the input using {{review.comment}} or similar binding.

AngularJS parses and evaluates the injected payload.

JavaScript executes in the context of the DOM → Alert, cookie theft, etc.

Juice Shop validates DOM-based execution and marks Bonus Payload Challenge as solved.

✅ Evidence of Success:
Alert popup appears as soon as the product/review page is loaded.

Juice Shop Score Board reflects the challenge as completed.

The payload is visible in the source, confirmed in the Elements tab.

📸 Proof of Concept Screenshot:
Filename: AngularJS-Expression-Injection.png

Must include the alert popup and Scoreboard “Bonus Payload Challenge” check.

🧠 Risk Assessment:
Vector	Description
Vulnerability	AngularJS template injection
CVSS Score Estimate	6.5–7.5 (Medium–High, based on app context)
Impact	Arbitrary JS execution → DOM compromise
Exploitable By	Any registered or anonymous user (via comment)
Potential Damage	XSS chain → session hijack, CSRF bypass, deface

🔐 Remediation Recommendations:
Disable expression evaluation in user-controlled DOM locations.

Use ng-bind or Angular's $sanitize service to strip unsafe input.

Escape all curly braces or disable interpolation for untrusted fields.

Migrate to modern frameworks or restrict legacy Angular templates.

📁 Suggested Folder Structure:
mathematica
Copy
Edit
OWASP-Juice-Shop-Bonus-Payload/
└── Method-3-AngularJS-Expression-Injection/
    ├── README.md
    ├── To-Do.md
    ├── payload.txt
    ├── AngularJS-Expression-Injection.png
🧾 Git Workflow:
bash
Copy
Edit
cd ~/juice-shop/OWASP-Juice-Shop-Bonus-Payload/Method-3-AngularJS-Expression-Injection
git add .
git commit -m "🚨 AngularJS Template Injection PoC – Payloads, README, Screenshot"
git push origin main
✅ Summary of Attack Outcome:
Discovered an AngularJS template injection point via user reviews.

Validated unsanitized rendering of {{ }} syntax in DOM.

Injected and executed a payload using constructor.constructor().

Captured evidence and documented methodology.

Demonstrated risk, exploitation, and remediation for dev teams.

AngularJS sandbox escapes remain one of the most elegant DOM-based XSS vectors — especially in legacy SPAs. Exploiting unsafe bindings is a silent killer.

Let me know if you want similar writeups for Method 4: Feedback Form, or to start a GitHub project board to track these vulnerabilities.


