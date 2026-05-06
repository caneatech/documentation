# Chapter 7 — Introduction to JavaScript

## 7.1 What is JavaScript?

**JavaScript** (JS) is a programming language that runs in the browser. While HTML defines *what* is on a page and CSS defines *how it looks*, JavaScript defines *what it does*.

With JavaScript you can:
- React to user actions (clicks, typing, scrolling)
- Update page content without reloading
- Validate form data
- Create animations and interactive features
- Fetch data from the internet

---

## 7.2 Adding JavaScript to a Web Page

There are three ways to add JavaScript:

### Option 1: Inline (not recommended)
```html
<button onclick="alert('Hello!')">Click Me</button>
```

### Option 2: Inside a `<script>` tag
```html
<body>
  <h1>My Page</h1>

  <script>
    console.log("JavaScript is running!");
  </script>
</body>
```

### Option 3: External `.js` file (best practice)
```html
<!-- In your HTML file -->
<script src="script.js"></script>
```

```javascript
// In script.js
console.log("Hello from an external file!");
```

> **Best practice:** Put your `<script>` tag just before the closing `</body>` tag so the HTML loads before JavaScript runs.

---

## 7.3 The Browser Console

The **console** is a developer tool built into your browser. It's where JavaScript can print messages and where you can test code.

**To open the console:**
- Windows/Linux: Press `F12` or `Ctrl + Shift + J`
- Mac: Press `Cmd + Option + J`

You'll use `console.log()` to print things to the console:

```javascript
console.log("Hello, World!");
console.log(42);
console.log(2 + 2);
```

---

## 7.4 Basic Output Methods

```javascript
// Print to the browser console (for developers)
console.log("Debug message");

// Show a popup alert to the user
alert("Welcome to my website!");

// Ask the user a question and get a response
let name = prompt("What is your name?");
alert("Hello, " + name + "!");

// Ask a yes/no question
let confirmed = confirm("Are you sure?");
console.log(confirmed); // true or false
```

---

## 7.5 JavaScript Syntax Basics

### Statements end with semicolons (optional but recommended)
```javascript
console.log("Hello");
let x = 5;
```

### JavaScript is case-sensitive
```javascript
let name = "Alice";   // This is fine
let Name = "Bob";     // This is a DIFFERENT variable
```

### Comments
```javascript
// This is a single-line comment

/*
  This is a
  multi-line comment
*/
```

---

## 7.6 Operators

```javascript
// Arithmetic
let a = 10 + 5;   // 15
let b = 10 - 3;   // 7
let c = 4 * 3;    // 12
let d = 10 / 2;   // 5
let e = 10 % 3;   // 1 (remainder)
let f = 2 ** 3;   // 8 (2 to the power of 3)

// String joining (concatenation)
let greeting = "Hello, " + "World!";

// Template literals (modern, cleaner way)
let user = "Alice";
let message = `Hello, ${user}! Welcome.`;
console.log(message); // "Hello, Alice! Welcome."
```

---

## 7.7 A Simple Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>My First JS Page</title>
  </head>
  <body>
    <h1>Welcome!</h1>
    <button onclick="sayHello()">Say Hello</button>

    <script>
      function sayHello() {
        alert("Hello! Thanks for clicking.");
      }
    </script>
  </body>
</html>
```

---

## ✅ Chapter Summary

- JavaScript adds interactivity and behavior to web pages.
- Add JS with a `<script>` tag, or link an external `.js` file.
- Use `console.log()` to print messages; open the browser console to see them.
- Use `alert()`, `prompt()`, and `confirm()` to interact with the user.
- Template literals (backticks) allow you to embed variables in strings: `` `Hello, ${name}!` ``

**Next:** [Chapter 8 — Variables and Data Types](chapter-08-js-variables.md)
