Method-3 – URL Parameter Injection (DOM-Based XSS). This is written in the tone of an offensive security researcher, suitable for formal documentation, pentest reports, or a GitHub portfolio.

markdown
Copy
Edit
### ✅ DOM-Based XSS – URL Parameter Injection (Method-2)

This document provides a structured and technically comprehensive breakdown of activities performed to successfully exploit a DOM-based Cross-Site Scripting (XSS) vulnerability in the OWASP Juice Shop application via the URL parameter `q`. The exploitation technique targets an insecure client-side injection point within the DOM and demonstrates how user-controlled input can lead to arbitrary JavaScript execution.

---

#### 🔹 [x] Recon: Identified Vulnerable URL Parameter (`q`)

Performed dynamic analysis of the Juice Shop web application by interacting with the `#/search` route. Appended a test parameter `?q=test` to the URL fragment and monitored DOM behavior using Chrome Developer Tools.

```bash
http://localhost:3000/#/search?q=test
Observed that the input test is directly reflected in the DOM with no encoding or sanitization.

Used the Elements tab to verify that the string was placed within the HTML document structure via a dynamic sink (innerHTML suspected).

Determined that this reflection occurs client-side and is processed entirely by the browser (SPA behavior).

No server-side validation or sanitization mechanisms were encountered.

The discovery of this reflection vector confirmed the injection point for a DOM-based XSS payload.

🔹 [x] Exploitation: Injected Working XSS Payload
Successfully crafted and deployed payloads capable of breaking out of the reflected HTML context and achieving JavaScript execution.

Payloads tested:

html
Copy
Edit
<script>alert(1)</script>
"><svg/onload=alert(1)>
"><img src=x onerror=alert(1)>
Encoded variants:

perl
Copy
Edit
?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E
?q=%22%3E%3Csvg%2Fonload%3Dalert(1)%3E
Key techniques employed:

Context breakout using quotes (" >)

Exploitation of event-based execution (onerror, onload)

Leveraged reflection into insecure sink (innerHTML)

Payloads were tested using various encodings to bypass any potential frontend input restrictions and ensure robust browser parsing.

🔹 [x] Validation: Confirmed JavaScript Execution in Browser
Executed each payload within the context of the vulnerable application and observed successful triggering of the embedded JavaScript.

Validation method:

Loaded malicious URL in browser

Observed immediate JavaScript alert(1) execution

No user interaction required

Verified through DevTools Console that the payload was reflected and parsed in-place in the DOM

Confirmed automatic solving of Juice Shop's Bonus Payload challenge (event-based flagging)

Additional validation (optional):

javascript
Copy
Edit
console.log(document.location.href);
console.log(document.cookie);
These confirmed the script's execution within the client browser environment and its potential to exfiltrate sensitive data such as session cookies.

🔹 [x] Documentation: Captured Screenshot as Evidence
Captured forensic evidence of successful XSS execution and challenge validation:

Included both the JavaScript alert box and the Juice Shop Score Board displaying the "Bonus Payload Challenge" as solved.

File saved with standardized naming:

mathematica
Copy
Edit
DOM-XSS-URL-Param-Payload.png
This artifact serves as proof-of-exploit and can be referenced in reporting or audit reviews.

🔹 [ ] Final Step: Push All Exploit Artifacts to GitHub Repository
Status: PENDING

All exploit documentation, including the README.md, To-Do.md, raw payload references, and the screenshot, are to be version-controlled and pushed to the central repository.

Repository Path:

mathematica
Copy
Edit
~/juice-shop/OWASP-Juice-Shop-Bonus-Payload/Method-2-URL-Param-Injection/
Git Workflow:

bash
Copy
Edit
cd ~/juice-shop/OWASP-Juice-Shop-Bonus-Payload/Method-2-URL-Param-Injection

# Stage changes
git add .

# Commit with clear message
git commit -m "📂 Method-2: DOM XSS via URL param – evidence, payloads, and writeup documented"

# Push to GitHub
git push origin main
Once completed, this ensures that all work related to this specific method is securely versioned, publicly visible (if applicable), and available for further collaboration, analysis, or review.

🔚 Summary
Task	Status
Identification of vulnerable parameter	✅ Done
Payload construction and execution	✅ Done
JavaScript behavior validated	✅ Done
Screenshot documentation captured	✅ Done
GitHub push and version control	⏳ Pending

🧠 This exploitation confirms that client-side frameworks like Angular or React, when misused, can be just as vulnerable as backend systems. DOM-based XSS often bypasses traditional WAFs and backend sanitizers — making them critical to identify during any modern web application assessment.

