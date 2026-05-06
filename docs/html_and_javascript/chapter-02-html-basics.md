# Chapter 2 — HTML Basics: Tags, Elements & Attributes

## 2.1 What is a Tag?

A **tag** is a keyword wrapped in angle brackets `< >`. Tags are the building blocks of HTML.

```html
<tagname>
```

Most tags come in **pairs** — an opening tag and a closing tag (with a `/`):

```html
<p>This is a paragraph.</p>
```

The opening tag is `<p>`, the closing tag is `</p>`, and everything between them is the *content*.

---

## 2.2 What is an Element?

An **element** is the complete unit: the opening tag + content + closing tag.

```
<h1>Welcome to My Site</h1>
 ↑         ↑                ↑
opening   content         closing
 tag                        tag
```

Some elements are **self-closing** — they don't wrap content and don't need a closing tag:

```html
<br>   <!-- line break -->
<img>  <!-- image -->
<hr>   <!-- horizontal rule / divider line -->
```

---

## 2.3 What is an Attribute?

**Attributes** give extra information about an element. They go inside the opening tag:

```html
<tagname attribute="value">
```

**Example:**

```html
<a href="https://www.google.com">Visit Google</a>
```

Here:
- `a` is the tag (for a hyperlink/anchor)
- `href` is the attribute name
- `"https://www.google.com"` is the attribute value

You can have multiple attributes on one element:

```html
<img src="photo.jpg" alt="A sunny beach" width="400">
```

---

## 2.4 Common HTML Tags

| Tag | Purpose | Example |
|-----|---------|---------|
| `<h1>` to `<h6>` | Headings (h1 = biggest, h6 = smallest) | `<h2>Section Title</h2>` |
| `<p>` | Paragraph | `<p>Some text here.</p>` |
| `<a>` | Hyperlink | `<a href="url">Click me</a>` |
| `<img>` | Image | `<img src="cat.jpg" alt="A cat">` |
| `<br>` | Line break | `Line one<br>Line two` |
| `<strong>` | Bold text | `<strong>Important!</strong>` |
| `<em>` | Italic text | `<em>Emphasis</em>` |
| `<div>` | Generic block container | `<div>...</div>` |
| `<span>` | Generic inline container | `<span>...</span>` |

---

## 2.5 HTML Comments

You can leave notes in your HTML that the browser will ignore. These are called **comments**:

```html
<!-- This is a comment. The browser won't show this. -->
<p>This text will show up.</p>
```

Comments are great for explaining your code to yourself or others.

---

## 2.6 Nesting Elements

Elements can be placed *inside* other elements. This is called **nesting**. Always close inner elements before outer ones:

✅ Correct:
```html
<p>This is <strong>bold</strong> text.</p>
```

❌ Incorrect (tags overlap):
```html
<p>This is <strong>bold</p></strong>
```

---

## ✅ Chapter Summary

- **Tags** are keywords in angle brackets like `<p>` and `</p>`.
- An **element** is the full tag + content combination.
- **Attributes** add extra information inside the opening tag.
- Elements can be **nested** inside each other.

**Next:** [Chapter 3 — Building a Web Page Structure](chapter-03-html-structure.md)
