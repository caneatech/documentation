# Chapter 6 — Forms and User Input

## 6.1 What is an HTML Form?

Forms let users enter and submit data — for example, a login page, a search bar, or a contact form.

```html
<form action="/submit" method="post">
  <!-- form fields go here -->
</form>
```

| Attribute | Purpose |
|-----------|---------|
| `action` | Where to send the form data (a URL or server endpoint) |
| `method` | How to send it: `get` (visible in URL) or `post` (hidden, more secure) |

---

## 6.2 Text Input

The most common form element. Use `<input type="text">`:

```html
<label for="name">Your Name:</label>
<input type="text" id="name" name="name" placeholder="Enter your name">
```

- `<label>` describes the input. The `for` attribute links it to the input's `id`.
- `placeholder` shows ghost text inside the field.
- `name` is how the data is identified when submitted.

---

## 6.3 Common Input Types

```html
<!-- Text -->
<input type="text" name="username">

<!-- Password (hides characters) -->
<input type="password" name="password">

<!-- Email (validates email format) -->
<input type="email" name="email">

<!-- Number -->
<input type="number" name="age" min="1" max="120">

<!-- Date -->
<input type="date" name="birthday">

<!-- Checkbox -->
<input type="checkbox" name="subscribe" id="subscribe">
<label for="subscribe">Subscribe to newsletter</label>

<!-- Radio buttons (only one can be selected) -->
<input type="radio" name="color" value="red" id="red">
<label for="red">Red</label>

<input type="radio" name="color" value="blue" id="blue">
<label for="blue">Blue</label>

<!-- File upload -->
<input type="file" name="avatar">

<!-- Hidden field (not shown to user) -->
<input type="hidden" name="user_id" value="42">
```

---

## 6.4 Textarea (Multi-line Text)

```html
<label for="message">Your Message:</label>
<textarea id="message" name="message" rows="5" cols="40" placeholder="Type here..."></textarea>
```

---

## 6.5 Dropdown (Select)

```html
<label for="country">Country:</label>
<select id="country" name="country">
  <option value="">-- Select a country --</option>
  <option value="gh">Ghana</option>
  <option value="ng">Nigeria</option>
  <option value="ke">Kenya</option>
</select>
```

Use `selected` to pre-select an option:
```html
<option value="gh" selected>Ghana</option>
```

---

## 6.6 The Submit Button

```html
<button type="submit">Send Form</button>

<!-- OR -->
<input type="submit" value="Send Form">
```

---

## 6.7 A Complete Contact Form

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Contact Us</title>
  </head>
  <body>
    <h1>Contact Us</h1>

    <form action="/send-message" method="post">

      <label for="fullname">Full Name:</label><br>
      <input type="text" id="fullname" name="fullname" placeholder="Jane Doe" required><br><br>

      <label for="email">Email:</label><br>
      <input type="email" id="email" name="email" placeholder="jane@example.com" required><br><br>

      <label for="subject">Subject:</label><br>
      <select id="subject" name="subject">
        <option value="general">General Inquiry</option>
        <option value="support">Support</option>
        <option value="feedback">Feedback</option>
      </select><br><br>

      <label for="message">Message:</label><br>
      <textarea id="message" name="message" rows="6" cols="40" required></textarea><br><br>

      <input type="checkbox" id="agree" name="agree" required>
      <label for="agree">I agree to the terms and conditions</label><br><br>

      <button type="submit">Send Message</button>

    </form>
  </body>
</html>
```

> **Note:** The `required` attribute means the user must fill in that field before submitting.

---

## ✅ Chapter Summary

- Forms are created with `<form>` and contain various input elements.
- `<input>` supports many types: text, email, password, checkbox, radio, file, and more.
- `<textarea>` is for multi-line text; `<select>` creates a dropdown.
- Always use `<label>` tags linked to inputs via `for` and `id`.
- The `required` attribute enforces that a field must be filled in.

**Next:** [Chapter 7 — Introduction to JavaScript](chapter-07-intro-to-javascript.md)
