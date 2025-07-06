🧪 OWASP Juice Shop - Bonus Payload Challenge
Method 1: UI Search Bar Injection – DOM-Based XSS via <iframe>
🔍 Overview
This technique exploits client-side JavaScript logic by injecting a malicious <iframe> through the search bar input of OWASP Juice Shop. Since the Angular frontend does not sanitize input properly in this specific scenario, it reflects raw HTML directly into the DOM, thus rendering the attacker-supplied <iframe> element.

This is a classic case of DOM-Based XSS (Type-0), where the payload never reaches the backend, and the vulnerability stems solely from frontend logic flaws.

🎯 Objective
Inject and render a valid <iframe> inside the DOM.

Trigger the Bonus Payload XSS challenge.

Display a SoundCloud Player using a trusted but externally hosted resource.

Understand DOM sinks, bypass filters, and simulate a real-world Angular client-side injection scenario.

📦 Target Environment Setup
✅ Pre-requisites
Component	Requirement
Host OS	Windows / macOS / Linux
Docker	Installed and configured
Browser	Chrome / Firefox (DevTools ON)
Access URL	http://localhost:3000
Juice Shop Image	bkimminich/juice-shop

🧰 Step-by-Step Exploitation Process
🔹 Step 1: Pull the Juice Shop Docker Image
Run this command in the terminal to fetch the latest stable Juice Shop image:

bash
Copy
Edit
docker pull bkimminich/juice-shop
🧠 Note: If already pulled, Docker will reuse the existing image.

🔹 Step 2: Run Juice Shop Container
bash
Copy
Edit
docker run --rm -p 3000:3000 bkimminich/juice-shop
--rm: Automatically remove the container after exit.

-p 3000:3000: Maps container port 3000 to host port 3000.

🔄 Expected Output in Terminal:

pgsql
Copy
Edit
> Ready for start up
Listening on port 3000
🧯 If you get port already in use error:

Run: lsof -i :3000 → to identify the process

Kill the process using: kill -9 <PID>

🔹 Step 3: Open the Application in Browser
📎 Navigate to:

arduino
Copy
Edit
http://localhost:3000
👁️‍🗨️ Expected Behavior:

OWASP Juice Shop home page should load with login banner or product list visible.

🔹 Step 4: Locate the UI Search Feature
🖱️ Click on the 🔍 Search icon on the top-right of the navigation bar.

📌 Tooltip will display: Search.

🔹 Step 5: Inject the Payload
🖊️ Paste the following <iframe> payload directly into the search input field:

html
Copy
Edit
<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" 
src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076"></iframe>
🔐 Important:

The src attribute is percent-encoded to prevent basic filtering.

Ensure there are no spaces or line breaks while pasting into the search bar.

🔹 Step 6: Execute the Payload
📲 Press Enter or Return key.

🧠 Expected Reaction:

The page should re-render.

The <iframe> will appear and embed the SoundCloud player into the page body.

Bonus Payload challenge will be marked as solved (scoreboard will update silently).

🖥️ What Happens Under the Hood
Angular parses the search query param and injects it into a dynamic section of the DOM.

Due to lack of sanitization and escaping, the <iframe> is treated as legitimate HTML.

Browser executes and renders the iframe pointing to the SoundCloud player.

🔄 Troubleshooting & Error Resolution
Symptom	Possible Cause	Resolution
❌ Nothing is rendered	Angular update disabled binding	Try refreshing and reinjecting. Use DevTools to inspect rendered DOM.
❌ HTML tags are escaped	DOM is sanitized by DomSanitizer or updated	Modify payload to use encoded <script>document.write(...)</script>
❌ Juice Shop not loading	Port 3000 blocked or Docker not running	Restart Docker. Ensure no VPN is blocking localhost.
❌ Challenge not marked solved	App’s internal challenge logic didn’t match	Clear cookies, restart app, reinject payload. Use incognito to avoid cached data.

📂 Proof of Concept Artifacts
Screenshot evidence:
Save browser-rendered SoundCloud iframe screen as:

bash
Copy
Edit
/poc/bonus-payload-ui-search.png
📎 Use DevTools Elements tab to validate DOM node injection:

html
Copy
Edit
<body>
  ...
  <iframe src="https://w.soundcloud.com/player/..."></iframe>
</body>
🔐 Remediation Recommendations
To prevent such DOM XSS via client-side inputs:

✅ Short-Term Fixes
Escape HTML entities before DOM rendering:

Use textContent instead of innerHTML.

Disallow <, >, and " characters from user inputs.

Enable basic client-side HTML filters.

🔐 Long-Term Fixes
Use Angular’s DomSanitizer to sanitize dynamic bindings:

ts
Copy
Edit
this.sanitized = this.sanitizer.bypassSecurityTrustHtml(userInput);
Apply a strong Content Security Policy (CSP):

h
Copy
Edit
Content-Security-Policy: default-src 'self'; frame-src 'none'; script-src 'self';
Enable XSS Protection headers:

http
Copy
Edit
X-XSS-Protection: 1; mode=block
🧠 Conclusion
This method showcases a pure DOM-based attack vector with no backend involvement. It emphasizes the importance of input sanitization in modern SPA applications. DOM-based XSS is dangerous because:

It bypasses traditional server-side validation layers.

It targets user trust by manipulating visible and interactive content.

Use this method to educate dev teams, enhance bug bounty reports, or train offensive security teams on modern XSS mechanics.
