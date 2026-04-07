# ◇ Introduction to EJS

An interactive Reveal.js presentation covering EJS (Embedded JavaScript) — from tag syntax and control flow through to layouts, partials, security, performance, and production Express integration.

## ▶ [Open the Presentation](https://brendanjameslynskey.github.io/Introduction_to_EJS/)

## 📄 [Markdown Version](presentation.md)

---

## Contents

| # | Topic | Description |
|---|-------|-------------|
| 01 | Title | Introduction to EJS — Embedded JavaScript |
| 02 | Agenda | Overview of all topics covered |
| 03 | What Is EJS? | History, philosophy, "just JavaScript", install & basic use |
| 04 | EJS Tags — The Complete Set | `<%= %>`, `<%- %>`, `<% %>`, `<%# %>`, `<%_ _%>` |
| 05 | Output Tags — Escaped vs Unescaped | `<%= %>` vs `<%- %>`, escaping internals, when to use each |
| 06 | Control Flow — Conditionals | if/else in templates, ternary patterns, conditional attributes |
| 07 | Control Flow — Loops | for, forEach, for...of, iterating objects, empty states |
| 08 | Passing Data to Templates | Express res.render, res.locals, app.locals, merge order |
| 09 | Layouts & Boilerplate | Manual includes, express-ejs-layouts, ejs-mate |
| 10 | Partials & Includes | include syntax, passing data, loop + include, nested includes |
| 11 | Custom Filters & Helpers | Utility functions, currency/date/truncate, global registration |
| 12 | Forms & User Input | Rendering forms, repopulating on error, CSRF tokens, selects |
| 13 | Security — XSS Prevention | Auto-escaping, sanitisation with DOMPurify, safe JSON embedding |
| 14 | Error Handling & Debugging | Common errors, defensive coding, error pages, debugging tips |
| 15 | Performance & Caching | Compilation pipeline, view cache, precompilation, benchmarks |
| 16 | EJS vs Other Engines | Comparison with Pug, Handlebars, Nunjucks |
| 17 | Real-World Project Structure | views/, partials/, layouts/, public/, app.js setup |
| 18 | Complete Working Example | Full Express + EJS BookShelf app with routes, views, partials |
| 19 | Summary & Next Steps | Core takeaways, best practices, resources, key packages |

---

## Slide Controls

| Action | Key |
|--------|-----|
| Next / Previous | `→` `←` or swipe |
| Overview | `Esc` |
| Fullscreen | `F` |
| Export to PDF | Append `?print-pdf` to URL, then print |

## Technology

[Reveal.js 4.6](https://revealjs.com) · [highlight.js](https://highlightjs.org) · Playfair Display + DM Sans + JetBrains Mono

Single self-contained `index.html` — no build step, no npm, no dependencies to install.

## References

- [EJS Official Site](https://ejs.co) — documentation and API reference
- [EJS GitHub Repository](https://github.com/mde/ejs) — source code and issues
- [Express.js Template Engines Guide](https://expressjs.com/en/guide/using-template-engines.html) — official Express integration docs
- [express-ejs-layouts](https://github.com/Soarez/express-ejs-layouts) — layout support for EJS
- [MDN Web Docs: Cross-Site Scripting (XSS)](https://developer.mozilla.org/en-US/docs/Glossary/Cross-site_scripting) — security background
- [DOMPurify](https://github.com/cure53/DOMPurify) — HTML sanitisation library
- [express-validator](https://express-validator.github.io/) — form validation middleware

## License

Educational use. Code examples provided as-is.
