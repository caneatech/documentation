# Chapter 3 — Building a Web Page Structure

## 3.1 The Standard HTML Template

Every HTML page follows a standard structure. Here is the template you should use every time:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title Here</title>
  </head>
  <body>

    <!-- Your visible content goes here -->

  </body>
</html>
```

Let's break down what's new here.

---

## 3.2 The `<head>` Section

The `<head>` is not visible on the page. It holds **metadata** — information *about* the page.

```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Awesome Page</title>
</head>
```

| Tag/Attribute | What It Does |
|---|---|
| `<meta charset="UTF-8">` | Supports all characters (letters, symbols, emoji) |
| `<meta name="viewport" ...>` | Makes the page look good on phones and tablets |
| `<title>` | Sets the tab title in the browser |

---

## 3.3 Semantic HTML — Giving Pages Meaning

HTML5 introduced **semantic elements** — tags that describe their purpose, not just how they look. Using them makes your pages easier to read and better for search engines.

```html
<body>

  <header>
    <h1>My Website</h1>
    <nav>
      <a href="index.html">Home</a>
      <a href="about.html">About</a>
    </nav>
  </header>

  <main>
    <section>
      <h2>Welcome</h2>
      <p>This is the main content area.</p>
    </section>

    <aside>
      <p>This is a sidebar or related info.</p>
    </aside>
  </main>

  <footer>
    <p>&copy; 2024 My Website</p>
  </footer>

</body>
```

### Key Semantic Elements

| Element | Purpose |
|---------|---------|
| `<header>` | Top section of the page or a section (logo, nav) |
| `<nav>` | Navigation links |
| `<main>` | The primary content of the page |
| `<section>` | A thematic grouping of content |
| `<article>` | A self-contained piece of content (like a blog post) |
| `<aside>` | Side content, related to the main content |
| `<footer>` | Bottom of the page (copyright, contact links) |

---

## 3.4 Block vs Inline Elements

HTML elements are either **block-level** or **inline**.

**Block elements** start on a new line and take up the full width available:
```html
<h1>I'm a block element</h1>
<p>So am I.</p>
<div>And me too.</div>
```

**Inline elements** stay within the flow of text and only take up as much space as needed:
```html
<p>This has <strong>bold</strong> and <em>italic</em> text inline.</p>
```

---

## 3.5 A Complete Example Page

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>My Portfolio</title>
  </head>
  <body>

    <header>
      <h1>Jane Doe</h1>
      <nav>
        <a href="#about">About</a>
        <a href="#projects">Projects</a>
      </nav>
    </header>

    <main>
      <section id="about">
        <h2>About Me</h2>
        <p>Hi! I'm learning HTML and loving it.</p>
      </section>

      <section id="projects">
        <h2>My Projects</h2>
        <p>Coming soon...</p>
      </section>
    </main>

    <footer>
      <p>&copy; 2024 Jane Doe</p>
    </footer>

  </body>
</html>
```

---

## ✅ Chapter Summary

- Every HTML page starts with `<!DOCTYPE html>` and has `<html>`, `<head>`, and `<body>`.
- The `<head>` holds invisible metadata; the `<body>` holds visible content.
- Semantic elements like `<header>`, `<main>`, and `<footer>` give structure and meaning.
- Elements are either block-level (start on new line) or inline (stay in flow of text).

**Next:** [Chapter 4 — Text, Links & Images](chapter-04-text-and-links.md)
