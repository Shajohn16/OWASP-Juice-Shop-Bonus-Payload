🧠 Understanding DOM-Based XSS via URL Parameter Injection
🔍 What is DOM-Based XSS?
DOM-based XSS occurs when the client-side JavaScript processes untrusted user-controlled data (e.g., from the URL) and directly injects it into the DOM (Document Object Model) without sanitization or encoding. Unlike reflected or stored XSS, the injection and execution happen entirely on the browser side, making it stealthier and more difficult to detect using server logs.

🧬 Attack Anatomy: Bonus Payload via URL
🧩 Objective:
Inject a crafted script in the URL query parameter that is reflected directly into a DOM sink without sanitization, triggering automatic JavaScript execution.

🧰 Step-by-Step Breakdown with Commands
📁 Step 1: Identify a Reflection Point
Open Juice Shop:

bash
Copy
Edit
http://localhost:3000/
Modify the URL by appending a query string:

bash
Copy
Edit
http://localhost:3000/#/search?q=test
Expected behavior:

You should see test reflected in the search results page or URL bar.

The app uses Angular, which handles routes with #/.

The q= parameter is processed by client-side JavaScript.

🔍 Check if input is inserted directly into the DOM:

Open DevTools → Inspect Element.

Look for where q is being placed (e.g., innerHTML, document.write, etc.).

💣 Step 2: Craft the Payload
Inject a self-executing script in the query string.

Payload:

bash
Copy
Edit
http://localhost:3000/#/search?q=<script>alert(document.cookie)</script>
This payload:

Places the payload in q=.

If Angular or custom JS inserts q into innerHTML or similar DOM sink, the script executes.

🧪 Expected Output:

A browser alert popup displaying document.cookie.

Confirms that JavaScript execution has occurred successfully.

🧪 Step 3: Try Obfuscation Variants (if filtering is applied)
If basic payloads are sanitized, try breaking out of context or encoding:

bash
Copy
Edit
?q="><svg/onload=alert(1)>
?q=<img src=x onerror=alert(1)>
?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E
🔐 Why it works:

These payloads break HTML attribute or tag structure and inject a new executable tag (<svg>, <img>, <script>).

onerror, onload handlers are widely used in DOM XSS for stealth.

🛡️ Technical Deep-Dive: Why Juice Shop is Vulnerable
Juice Shop's search feature parses q= from the URL client-side and reflects it directly. If the implementation looks like this:

js
Copy
Edit
document.getElementById('search').innerHTML = location.href.split('q=')[1];
❗ Problem: Direct assignment to innerHTML = XSS risk if unsanitized.

Secure alternative:

js
Copy
Edit
document.getElementById('search').textContent = decodeURIComponent(query);
✅ Validation & Bonus Payload Completion
Once the payload executes, Juice Shop will display a solved challenge:

"Bonus Payload Challenge Completed" or a visual tick.

Confirm via:

bash
Copy
Edit
http://localhost:3000/#/score-board
🧼 Post Exploitation Recommendation (Dev Perspective)
Risk	Recommendation
Direct DOM injection	Use safe sinks like textContent instead of innerHTML
No input validation	Sanitize query parameters before rendering
Poor encoding	Encode HTML entities before inserting into DOM

📌 Summary
Element	Description
Vulnerability	DOM-based XSS via query string
Sink	innerHTML, document.write
Payload	<script>, <svg>, <img onerror>
Result	JavaScript execution on victim’s browser
Fix	Sanitize + encode input, avoid unsafe DOM sinks

