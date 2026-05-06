# Chapter 9 — Functions and Control Flow

## 9.1 Conditional Statements (if / else)

Conditionals let your code make decisions:

```javascript
let age = 20;

if (age >= 18) {
  console.log("You are an adult.");
} else {
  console.log("You are a minor.");
}
```

### else if (multiple conditions)
```javascript
let score = 75;

if (score >= 90) {
  console.log("Grade: A");
} else if (score >= 80) {
  console.log("Grade: B");
} else if (score >= 70) {
  console.log("Grade: C");
} else {
  console.log("Grade: F");
}
```

### Comparison Operators

| Operator | Meaning |
|----------|---------|
| `==` | Equal to (loose) |
| `===` | Equal to (strict — checks type too) |
| `!=` | Not equal |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal |
| `<=` | Less than or equal |

> **Always prefer `===` over `==`** to avoid unexpected results.

### Logical Operators

```javascript
// AND (&&) — both must be true
if (age >= 18 && hasID === true) {
  console.log("Access granted.");
}

// OR (||) — at least one must be true
if (day === "Saturday" || day === "Sunday") {
  console.log("It's the weekend!");
}

// NOT (!) — reverses true/false
if (!isLoggedIn) {
  console.log("Please log in.");
}
```

---

## 9.2 The Switch Statement

Use `switch` when comparing one value against many options:

```javascript
let day = "Monday";

switch (day) {
  case "Monday":
    console.log("Start of the work week.");
    break;
  case "Friday":
    console.log("Almost the weekend!");
    break;
  case "Saturday":
  case "Sunday":
    console.log("Weekend!");
    break;
  default:
    console.log("A regular weekday.");
}
```

Always include `break` to prevent falling through to the next case.

---

## 9.3 Loops

Loops repeat a block of code multiple times.

### for loop
```javascript
for (let i = 0; i < 5; i++) {
  console.log("Count: " + i);
}
// Output: Count: 0, Count: 1, Count: 2, Count: 3, Count: 4
```

### while loop
```javascript
let count = 0;
while (count < 3) {
  console.log("Hello!");
  count++;
}
```

### for...of (loop through arrays)
```javascript
let fruits = ["apple", "banana", "mango"];
for (let fruit of fruits) {
  console.log(fruit);
}
```

### forEach (array method with a function)
```javascript
let numbers = [1, 2, 3, 4, 5];
numbers.forEach(function(num) {
  console.log(num * 2);
});
```

---

## 9.4 Functions

A **function** is a reusable block of code that you can call whenever needed.

### Declaring a Function

```javascript
function greet(name) {
  console.log("Hello, " + name + "!");
}

greet("Alice");  // Hello, Alice!
greet("Bob");    // Hello, Bob!
```

### Returning a Value

```javascript
function add(a, b) {
  return a + b;
}

let result = add(3, 5);
console.log(result);  // 8
```

### Default Parameters

```javascript
function greet(name = "stranger") {
  console.log("Hello, " + name + "!");
}

greet();          // Hello, stranger!
greet("Alice");   // Hello, Alice!
```

---

## 9.5 Arrow Functions (Modern Shorthand)

Arrow functions are a shorter way to write functions:

```javascript
// Traditional function
function square(x) {
  return x * x;
}

// Arrow function (same thing)
const square = (x) => x * x;

console.log(square(4));  // 16
```

More examples:
```javascript
const greet = (name) => `Hello, ${name}!`;
const add = (a, b) => a + b;
const sayHi = () => console.log("Hi!");
```

---

## 9.6 Putting It All Together

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Grade Calculator</title>
</head>
<body>
  <h1>Grade Calculator</h1>
  <button onclick="calculateGrade()">Calculate My Grade</button>

  <script>
    function calculateGrade() {
      let score = Number(prompt("Enter your score (0-100):"));

      if (isNaN(score) || score < 0 || score > 100) {
        alert("Please enter a valid score between 0 and 100.");
        return;
      }

      let grade;
      if (score >= 90) {
        grade = "A";
      } else if (score >= 80) {
        grade = "B";
      } else if (score >= 70) {
        grade = "C";
      } else if (score >= 60) {
        grade = "D";
      } else {
        grade = "F";
      }

      alert(`Your score is ${score}. Your grade is: ${grade}`);
    }
  </script>
</body>
</html>
```

---

## ✅ Chapter Summary

- `if / else if / else` lets your code make decisions based on conditions.
- Use `===` for comparison (not `==`).
- `for`, `while`, and `for...of` loops repeat code.
- Functions are reusable blocks of code declared with `function` or arrow `=>` syntax.
- Functions can accept **parameters** and **return** values.

**Next:** [Chapter 10 — JavaScript and the DOM](chapter-10-js-dom.md)
