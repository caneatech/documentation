# Chapter 1 — What is HTML?

## 1.1 The Web in a Nutshell

Every website you visit is made of three core technologies:

- **HTML** — defines the *structure* and *content* (the skeleton)
- **CSS** — defines the *appearance* (the skin and style)
- **JavaScript** — defines the *behavior* (the muscles and brain)

This guide focuses on **HTML** and **JavaScript**.

---

## 1.2 What Does HTML Stand For?

**HTML** stands for **HyperText Markup Language**.

- **HyperText** means text that contains links to other text or pages.
- **Markup Language** means you use special codes (called *tags*) to annotate your content and tell the browser how to display it.

HTML is **not** a programming language — it's a *markup* language. It describes content, it doesn't give instructions to a computer the way a program does.

---

## 1.3 How Does a Browser Use HTML?

When you visit a website, your browser (Chrome, Firefox, Safari, etc.) does the following:

1. Requests the HTML file from a server (or reads it from your computer).
2. Reads the HTML from top to bottom.
3. Renders (draws) the page on your screen based on what it finds.

Think of HTML as a **recipe** and the browser as a **chef** — the browser reads your instructions and produces the final dish (the webpage).

---

## 1.4 Your First HTML File

Let's create a simple webpage. Open a text editor, type the following, and save it as `index.html`:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My First Page</title>
  </head>
  <body>
    <h1>Hello, World!</h1>
    <p>This is my first web page.</p>
  </body>
</html>
```

Now open that file in your browser. You should see a heading and a paragraph!

---

## 1.5 What Each Part Means

| Part | Meaning |
|------|---------|
| `<!DOCTYPE html>` | Tells the browser this is a modern HTML5 document |
| `<html>` | The root (start) of the HTML document |
| `<head>` | Contains invisible info about the page (like the title) |
| `<title>` | Text shown on the browser tab |
| `<body>` | Everything visible on the page goes here |
| `<h1>` | A large heading |
| `<p>` | A paragraph of text |

---

## ✅ Chapter Summary

- HTML stands for HyperText Markup Language.
- It defines the *structure* of a web page using *tags*.
- Browsers read HTML and render it visually.
- Every HTML page starts with `<!DOCTYPE html>` and has a `<head>` and a `<body>`.

**Next:** [Chapter 2 — HTML Basics: Tags, Elements & Attributes](chapter-02-html-basics.md)
