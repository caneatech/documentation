# Chapter 5 — Lists and Tables

## 5.1 Unordered Lists (Bullet Points)

Use `<ul>` (unordered list) for items where order doesn't matter. Each item goes in a `<li>` (list item) tag:

```html
<ul>
  <li>Apples</li>
  <li>Bananas</li>
  <li>Oranges</li>
</ul>
```

This renders as:
- Apples
- Bananas
- Oranges

---

## 5.2 Ordered Lists (Numbered)

Use `<ol>` (ordered list) when the order matters:

```html
<ol>
  <li>Preheat the oven to 180°C</li>
  <li>Mix the ingredients</li>
  <li>Bake for 30 minutes</li>
</ol>
```

This renders as:
1. Preheat the oven to 180°C
2. Mix the ingredients
3. Bake for 30 minutes

---

## 5.3 Nested Lists

You can put a list inside another list:

```html
<ul>
  <li>Fruits
    <ul>
      <li>Apple</li>
      <li>Mango</li>
    </ul>
  </li>
  <li>Vegetables
    <ul>
      <li>Carrot</li>
      <li>Broccoli</li>
    </ul>
  </li>
</ul>
```

---

## 5.4 Description Lists

A `<dl>` (description list) pairs terms with definitions:

```html
<dl>
  <dt>HTML</dt>
  <dd>HyperText Markup Language — the structure of web pages.</dd>

  <dt>CSS</dt>
  <dd>Cascading Style Sheets — the styling of web pages.</dd>
</dl>
```

- `<dt>` = description term
- `<dd>` = description detail

---

## 5.5 Tables

Tables are used to display data in rows and columns. The basic structure:

```html
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Age</th>
      <th>City</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Alice</td>
      <td>28</td>
      <td>Accra</td>
    </tr>
    <tr>
      <td>Bob</td>
      <td>34</td>
      <td>Lagos</td>
    </tr>
  </tbody>
</table>
```

### Table Tags Explained

| Tag | Meaning |
|-----|---------|
| `<table>` | The entire table |
| `<thead>` | The header section of the table |
| `<tbody>` | The body (data rows) of the table |
| `<tr>` | A table row |
| `<th>` | A header cell (bold and centered by default) |
| `<td>` | A data cell |

---

## 5.6 Spanning Columns and Rows

You can make a cell stretch across multiple columns or rows:

```html
<!-- Spanning across 2 columns -->
<td colspan="2">This cell spans two columns</td>

<!-- Spanning across 2 rows -->
<td rowspan="2">This cell spans two rows</td>
```

**Example:**

```html
<table>
  <tr>
    <th colspan="2">Full Name</th>
    <th>Score</th>
  </tr>
  <tr>
    <td>Alice</td>
    <td>Smith</td>
    <td>95</td>
  </tr>
</table>
```

---

## ✅ Chapter Summary

- `<ul>` creates bulleted lists; `<ol>` creates numbered lists; items go in `<li>`.
- `<dl>`, `<dt>`, and `<dd>` create description/definition lists.
- Tables use `<table>`, `<tr>`, `<th>`, and `<td>`.
- Use `<thead>` and `<tbody>` to organize your table structure.
- `colspan` and `rowspan` let a cell span multiple columns or rows.

**Next:** [Chapter 6 — Forms and User Input](chapter-06-forms.md)
