# Chapter 10 — JavaScript and the DOM

## 10.1 What is the DOM?

When a browser loads your HTML, it creates a **Document Object Model (DOM)** — a live, in-memory tree of all the elements on your page.

JavaScript can:
- **Find** elements on the page
- **Read** their content or attributes
- **Change** text, styles, or HTML
- **React** to events like clicks, typing, and scrolling

Think of the DOM as a live version of your HTML that JavaScript can poke and prod.

---

## 10.2 Selecting Elements

To work with an element, you first need to **select** it.

```javascript
// By ID (returns one element)
let title = document.getElementById("main-title");

// By CSS class (returns a collection)
let buttons = document.getElementsByClassName("btn");

// By tag name (returns a collection)
let paragraphs = document.getElementsByTagName("p");

// CSS selector — most flexible (returns first match)
let hero = document.querySelector(".hero");

// CSS selector — returns ALL matches
let allLinks = document.querySelectorAll("a");
```

> **Best practice:** Use `querySelector` and `querySelectorAll` — they work like CSS selectors and are the most flexible.

---

## 10.3 Changing Content

```html
<h1 id="greeting">Hello</h1>
<p id="info">Some info here.</p>
```

```javascript
// Change text content (safe — treats input as plain text)
document.getElementById("greeting").textContent = "Welcome, Alice!";

// Change HTML content (can include tags)
document.getElementById("info").innerHTML = "Click <strong>here</strong> to learn more.";
```

---

## 10.4 Changing Styles

```html
<p id="message">I will change color!</p>
```

```javascript
let msg = document.getElementById("message");
msg.style.color = "red";
msg.style.fontSize = "24px";
msg.style.backgroundColor = "yellow";
```

> CSS property names in JavaScript use **camelCase**: `background-color` → `backgroundColor`, `font-size` → `fontSize`.

---

## 10.5 Changing Attributes

```javascript
let link = document.querySelector("a");
link.setAttribute("href", "https://www.example.com");
link.setAttribute("target", "_blank");

let img = document.querySelector("img");
img.src = "new-image.jpg";
img.alt = "A new description";
```

---

## 10.6 Adding and Removing CSS Classes

Managing classes is better than setting inline styles directly:

```javascript
let box = document.getElementById("box");

// Add a class
box.classList.add("highlight");

// Remove a class
box.classList.remove("highlight");

// Toggle a class (adds if absent, removes if present)
box.classList.toggle("active");

// Check if a class exists
if (box.classList.contains("active")) {
  console.log("Box is active!");
}
```

---

## 10.7 Handling Events

**Events** are things that happen: a click, a key press, a form submission.

### Method 1: `addEventListener` (recommended)

```html
<button id="myBtn">Click Me</button>
```

```javascript
let btn = document.getElementById("myBtn");

btn.addEventListener("click", function() {
  alert("Button was clicked!");
});
```

### Common Events

| Event | Triggered when... |
|-------|------------------|
| `click` | User clicks an element |
| `mouseover` | Mouse moves over an element |
| `mouseout` | Mouse leaves an element |
| `keydown` | A key is pressed |
| `input` | Input field value changes |
| `submit` | A form is submitted |
| `load` | Page finishes loading |

---

## 10.8 Creating and Removing Elements

```javascript
// Create a new element
let newParagraph = document.createElement("p");
newParagraph.textContent = "I was added by JavaScript!";

// Add it to the page
document.body.appendChild(newParagraph);

// Remove an element
let oldElement = document.getElementById("old-item");
oldElement.remove();
```

---

## 10.9 A Complete Interactive Example

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>To-Do List</title>
  <style>
    .done { text-decoration: line-through; color: gray; }
  </style>
</head>
<body>
  <h1>My To-Do List</h1>

  <input type="text" id="taskInput" placeholder="Enter a task...">
  <button id="addBtn">Add Task</button>

  <ul id="taskList"></ul>

  <script>
    const addBtn = document.getElementById("addBtn");
    const taskInput = document.getElementById("taskInput");
    const taskList = document.getElementById("taskList");

    addBtn.addEventListener("click", function() {
      const taskText = taskInput.value.trim();

      if (taskText === "") {
        alert("Please enter a task!");
        return;
      }

      // Create a new list item
      const li = document.createElement("li");
      li.textContent = taskText;

      // Click to mark as done
      li.addEventListener("click", function() {
        li.classList.toggle("done");
      });

      taskList.appendChild(li);
      taskInput.value = ""; // Clear the input
    });

    // Allow pressing Enter to add a task
    taskInput.addEventListener("keydown", function(event) {
      if (event.key === "Enter") {
        addBtn.click();
      }
    });
  </script>
</body>
</html>
```

Save this as an `.html` file and open it in your browser — you'll have a working to-do list!

---

## ✅ Chapter Summary

- The **DOM** is JavaScript's live representation of your HTML page.
- Select elements with `querySelector` / `querySelectorAll`.
- Change content with `.textContent` or `.innerHTML`.
- Change styles with `.style.propertyName` or manage classes with `.classList`.
- React to user actions with `.addEventListener("event", function)`.
- Create new elements with `document.createElement()` and add them with `.appendChild()`.

---

## 🎉 Congratulations!

You've completed the Introduction to HTML and JavaScript guide! Here's what you've learned:

| Topic | Chapter |
|-------|---------|
| What HTML is and how browsers use it | Ch. 1 |
| Tags, elements, and attributes | Ch. 2 |
| Page structure and semantic HTML | Ch. 3 |
| Text, links, and images | Ch. 4 |
| Lists and tables | Ch. 5 |
| Forms and user input | Ch. 6 |
| What JavaScript is and how to use it | Ch. 7 |
| Variables and data types | Ch. 8 |
| Functions and control flow | Ch. 9 |
| The DOM and interactivity | Ch. 10 |

### What to Learn Next
- **CSS** — style your HTML pages with colors, fonts, and layouts
- **CSS Flexbox & Grid** — build responsive layouts
- **Fetch API** — load data from the internet
- **A JavaScript framework** — React, Vue, or Svelte for building apps

Keep building — the best way to learn is to make things! 🚀
