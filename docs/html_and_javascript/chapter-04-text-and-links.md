# Chapter 4 — Text, Links & Images

## 4.1 Headings

HTML has six levels of headings, from `<h1>` (most important, largest) to `<h6>` (least important, smallest):

```html
<h1>Main Page Title</h1>
<h2>Section Heading</h2>
<h3>Sub-section Heading</h3>
<h4>Smaller Heading</h4>
<h5>Even Smaller</h5>
<h6>Smallest Heading</h6>
```

> **Best practice:** Use only one `<h1>` per page. It should describe the main topic of the page.

---

## 4.2 Paragraphs and Text Formatting

```html
<p>This is a regular paragraph of text.</p>

<p>You can make text <strong>bold</strong> or <em>italic</em>.</p>

<p>You can also <mark>highlight text</mark> or show <del>deleted text</del>.</p>

<p>Use <sub>subscript</sub> or <sup>superscript</sup> for math: H<sub>2</sub>O, x<sup>2</sup></p>
```

### Text Formatting Tags

| Tag | Result |
|-----|--------|
| `<strong>` | **Bold** |
| `<em>` | *Italic* |
| `<mark>` | Highlighted |
| `<del>` | ~~Strikethrough~~ |
| `<u>` | Underline |
| `<sub>` | Subscript (H₂O) |
| `<sup>` | Superscript (x²) |
| `<code>` | `Inline code` |

---

## 4.3 Line Breaks and Horizontal Rules

```html
<p>This is line one.<br>This is line two.</p>

<hr>

<p>This paragraph is below the divider line.</p>
```

- `<br>` creates a line break within text.
- `<hr>` draws a horizontal line (used to separate sections).

---

## 4.4 Links (Anchor Tags)

Links are created with the `<a>` (anchor) tag:

```html
<a href="https://www.example.com">Visit Example.com</a>
```

### Linking to Another Page on Your Site

```html
<a href="about.html">About Me</a>
```

### Opening a Link in a New Tab

```html
<a href="https://www.google.com" target="_blank">Open Google in New Tab</a>
```

### Linking to a Section on the Same Page

First, give the section an `id`:
```html
<h2 id="contact">Contact Me</h2>
```

Then link to it:
```html
<a href="#contact">Go to Contact Section</a>
```

### Linking an Email Address

```html
<a href="mailto:hello@example.com">Send me an email</a>
```

---

## 4.5 Images

Images are added with the self-closing `<img>` tag:

```html
<img src="photo.jpg" alt="A description of the photo">
```

### Key Image Attributes

| Attribute | Purpose |
|-----------|---------|
| `src` | The path or URL of the image file |
| `alt` | Alternative text (shown if image fails to load; important for accessibility) |
| `width` | Width in pixels |
| `height` | Height in pixels |

### Examples

```html
<!-- Local image -->
<img src="images/cat.jpg" alt="A fluffy orange cat" width="300">

<!-- Image from the web -->
<img src="https://example.com/sunset.jpg" alt="A beautiful sunset">
```

> **Always include the `alt` attribute.** It helps screen readers describe images to visually impaired users.

---

## 4.6 Making an Image a Link

Wrap the `<img>` tag inside an `<a>` tag:

```html
<a href="https://www.example.com">
  <img src="logo.png" alt="Example Company Logo">
</a>
```

---

## ✅ Chapter Summary

- Use `<h1>` to `<h6>` for headings — only one `<h1>` per page.
- Use tags like `<strong>`, `<em>`, and `<mark>` to format text.
- `<a href="...">` creates links; use `target="_blank"` to open in a new tab.
- `<img src="..." alt="...">` embeds images. Always include an `alt` attribute.

**Next:** [Chapter 5 — Lists and Tables](chapter-05-lists-and-tables.md)
