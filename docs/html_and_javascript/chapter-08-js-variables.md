# Chapter 8 — Variables and Data Types

## 8.1 What is a Variable?

A **variable** is a named container that stores a value. Think of it like a labeled box.

```javascript
let age = 25;
let name = "Alice";
```

Now whenever you use `age`, JavaScript knows you mean `25`.

---

## 8.2 Declaring Variables

There are three keywords to declare a variable:

| Keyword | When to Use |
|---------|-------------|
| `let` | For values that may change (most common) |
| `const` | For values that should never change |
| `var` | Older way — avoid it in modern code |

```javascript
let score = 0;        // can change later
score = 10;           // ✅ allowed

const PI = 3.14159;   // cannot change
PI = 3;               // ❌ Error!
```

> **Rule of thumb:** Use `const` by default. Use `let` when you know the value will change.

---

## 8.3 Data Types

JavaScript has several built-in data types:

### String (text)
```javascript
let firstName = "Alice";
let lastName = 'Smith';             // single or double quotes both work
let sentence = `My name is ${firstName} ${lastName}.`;  // template literal
```

### Number
```javascript
let age = 30;
let price = 9.99;
let temperature = -5;
```

### Boolean (true/false)
```javascript
let isLoggedIn = true;
let hasAccount = false;
```

### Null (intentionally empty)
```javascript
let selectedItem = null;  // no value chosen yet
```

### Undefined (not yet assigned)
```javascript
let result;
console.log(result);  // undefined
```

### Array (a list of values)
```javascript
let colors = ["red", "green", "blue"];
let numbers = [1, 2, 3, 4, 5];
let mixed = ["Alice", 30, true];
```

### Object (a collection of key-value pairs)
```javascript
let person = {
  name: "Alice",
  age: 30,
  city: "Accra"
};
```

---

## 8.4 Working with Strings

```javascript
let greeting = "Hello, World!";

// Length of a string
console.log(greeting.length);          // 13

// Convert to uppercase / lowercase
console.log(greeting.toUpperCase());   // "HELLO, WORLD!"
console.log(greeting.toLowerCase());   // "hello, world!"

// Check if a string includes a word
console.log(greeting.includes("World")); // true

// Replace part of a string
console.log(greeting.replace("World", "Alice")); // "Hello, Alice!"

// Slice out a portion
console.log(greeting.slice(0, 5));     // "Hello"
```

---

## 8.5 Working with Arrays

```javascript
let fruits = ["apple", "banana", "cherry"];

// Access an item (index starts at 0)
console.log(fruits[0]);  // "apple"
console.log(fruits[2]);  // "cherry"

// Number of items
console.log(fruits.length);  // 3

// Add to the end
fruits.push("mango");

// Remove from the end
fruits.pop();

// Find index of an item
console.log(fruits.indexOf("banana"));  // 1

// Loop through all items
for (let fruit of fruits) {
  console.log(fruit);
}
```

---

## 8.6 Working with Objects

```javascript
let car = {
  brand: "Toyota",
  model: "Camry",
  year: 2022,
  isElectric: false
};

// Access a property
console.log(car.brand);       // "Toyota"
console.log(car["year"]);     // 2022

// Update a property
car.year = 2024;

// Add a new property
car.color = "blue";

// Loop through properties
for (let key in car) {
  console.log(key + ": " + car[key]);
}
```

---

## 8.7 Checking Data Types

Use `typeof` to check what type a variable is:

```javascript
console.log(typeof "hello");   // "string"
console.log(typeof 42);        // "number"
console.log(typeof true);      // "boolean"
console.log(typeof [1,2,3]);   // "object"
console.log(typeof null);      // "object" (a known quirk of JavaScript)
```

---

## ✅ Chapter Summary

- Use `let` for changing values and `const` for fixed values.
- JavaScript has several data types: string, number, boolean, null, undefined, array, and object.
- Arrays hold ordered lists accessed by index (starting at 0).
- Objects hold key-value pairs accessed by property name.
- Use `typeof` to check a variable's data type.

**Next:** [Chapter 9 — Functions and Control Flow](chapter-09-js-functions.md)
