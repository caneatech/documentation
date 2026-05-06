# INTRODUCTION
## Variable Declaration
```vb
Dim myVariable
```

To assign a value:
```vb
Dim myVariable
myVariable = "Hello"
```

Or on one line using a colon:
```vb
Dim myVariable: myVariable = "Hello"
```
VBScript is loosely typed — all variables are `Variant` by default, so no type declaration is needed.

## Array Declaration and assignment
```vb
Dim myArray(2)
myArray(0) = "One"
myArray(1) = "Two"
myArray(2) = "Three"
```

Arrays are **zero-based** and the number in parentheses is the **upper bound**, not the size — so `(2)` gives you 3 slots.

**Dynamic array:**
```vb
Dim myArray()
ReDim myArray(2)
```

Use `ReDim Preserve` to resize without losing existing data:
```vb
ReDim Preserve myArray(5)
```
## Conditional Statements
**If / ElseIf / Else**
```vb
If x = 1 Then
    ' code
ElseIf x = 2 Then
    ' code
Else
    ' code
End If
```

**Single line:**
```vb
If x = 1 Then Response.Write "One"
```

**Select Case** (cleaner for multiple conditions):
```vb
Select Case x
    Case 1
        ' code
    Case 2
        ' code
    Case Else
        ' code
End Select
```


## Loops
**For / Next**
```vb
For i = 0 To 5
    ' code
Next
```

**Step** (increment by custom amount):
```vb
For i = 0 To 10 Step 2
    ' code
Next
```

**For Each** (iterate over array/collection):
```vb
For Each item In myArray
    ' code
Next
```**Common String Functions**
```vb
Len("Hello")            ' Length          = 5
UCase("Hello")          ' Uppercase       = "HELLO"
LCase("Hello")          ' Lowercase       = "hello"
Trim("  Hello  ")       ' Remove spaces   = "Hello"
LTrim("  Hello")        ' Left trim       = "Hello"
RTrim("Hello  ")        ' Right trim      = "Hello"
```

**Searching & Replacing**
```vb
InStr("Hello", "ell")          ' Find position  = 2
Replace("Hello", "ell", "ELL") ' Replace        = "HELLo"
```

**Substrings**
```vb
Left("Hello", 3)        ' From left       = "Hel"
Right("Hello", 3)       ' From right      = "llo"
Mid("Hello", 2, 3)      ' Mid(str, start, length) = "ell"
```

**Split & Join**
```vb
arr = Split("One,Two,Three", ",")  ' String to array
str = Join(arr, ",")               ' Array to string
```

**Type Conversion**
```vb
CStr(123)       ' Number to string  = "123"
CInt("123")     ' String to number  = 123
```

**Do While** (runs while condition is true):
```vb
Do While x < 10
    ' code
Loop
```

**Do Until** (runs until condition is true):
```vb
Do Until x = 10
    ' code
Loop
```

Use `Exit For` or `Exit Do` to break out of any loop early.

## Arithmetic, Comparison and Logical Operators
**Arithmetic Operators**
```vb
x = 10 + 2   ' Addition       = 12
x = 10 - 2   ' Subtraction    = 8
x = 10 * 2   ' Multiplication = 20
x = 10 / 2   ' Division       = 5
x = 10 ^ 2   ' Exponent       = 100
x = 10 Mod 3 ' Remainder      = 1
```

**Comparison Operators**
```vb
x = 1
x = 1   ' Equal
x <> 1  ' Not equal
x > 1   ' Greater than
x < 1   ' Less than
x >= 1  ' Greater than or equal
x <= 1  ' Less than or equal
```

**Logical Operators**
```vb
If x = 1 And y = 2 Then   ' Both must be true
If x = 1 Or y = 2 Then    ' At least one true
If Not x = 1 Then          ' Negation
```

**String Concatenation**
```vb
x = "Hello" & " " & "World"  ' = "Hello World"
```

## String Manipulation
**Common String Functions**
```vb
Len("Hello")            ' Length          = 5
UCase("Hello")          ' Uppercase       = "HELLO"
LCase("Hello")          ' Lowercase       = "hello"
Trim("  Hello  ")       ' Remove spaces   = "Hello"
LTrim("  Hello")        ' Left trim       = "Hello"
RTrim("Hello  ")        ' Right trim      = "Hello"
```

**Searching & Replacing**
```vb
InStr("Hello", "ell")          ' Find position  = 2
Replace("Hello", "ell", "ELL") ' Replace        = "HELLo"
```

**Substrings**
```vb
Left("Hello", 3)        ' From left       = "Hel"
Right("Hello", 3)       ' From right      = "llo"
Mid("Hello", 2, 3)      ' Mid(str, start, length) = "ell"
```

**Split & Join**
```vb
arr = Split("One,Two,Three", ",")  ' String to array
str = Join(arr, ",")               ' Array to string
```

**Type Conversion**
```vb
CStr(123)       ' Number to string  = "123"
CInt("123")     ' String to number  = 123
```

## Functions and Sub
**Sub** (no return value):
```vb
Sub Greet(name)
    Response.Write "Hello " & name
End Sub

' Call it
Greet "John"
Call Greet("John")
```

**Function** (returns a value):
```vb
Function Add(a, b)
    Add = a + b
End Function

' Call it
result = Add(5, 10)  ' = 15
```

**Optional: Exit early**
```vb
Sub Greet(name)
    If name = "" Then Exit Sub
    Response.Write "Hello " & name
End Sub

Function Add(a, b)
    If a = "" Then Exit Function
    Add = a + b
End Function
```

Key difference — a `Function` returns a value by assigning it to **the function's own name**, a `Sub` does not return anything.

## Key notes and Tricks in VB Script
**Variables & Types**
```vb
' VBScript is loosely typed, but watch out for implicit conversions
Dim x: x = "5" + 5   ' = 10  (string coerced to number)
Dim y: y = "5" & 5   ' = "55" (number coerced to string)
```

**Null & Empty Checks**
```vb
IsNull(x)     ' True if Null
IsEmpty(x)    ' True if never assigned
IsNumeric(x)  ' True if numeric
IsArray(x)    ' True if array
```

**Error Handling**
```vb
On Error Resume Next   ' Suppress errors, continue execution
    ' risky code here
    If Err.Number <> 0 Then
        Response.Write Err.Description
        Err.Clear
    End If
On Error GoTo 0        ' Always re-enable error handling after
```

**String Comparisons**
```vb
' Case-insensitive comparison
If LCase(x) = LCase(y) Then

' Safer than = for trimmed input
If Trim(CStr(x)) = Trim(CStr(y)) Then
```

**Recordset Tips**
```vb
' Always check EOF before reading
If Not rst.EOF Then rst.MoveFirst

' Always close and destroy objects
rst.Close
Set rst = Nothing
```

**Common Pitfalls**
```vb
' Declare variables - avoid typos going undetected
Option Explicit   ' Forces all variables to be declared with Dim

' Integer division vs float division
x = 7 \ 2    ' = 3  (integer division, use backslash)
x = 7 / 2    ' = 3.5 (float division, use forward slash)

' Nothing vs Empty vs Null — they are NOT the same
Set obj = Nothing   ' For objects
x = Empty           ' For uninitialized variables
x = Null            ' For unknown/missing data
```

**Useful Shortcuts**
```vb
' One-line declare and assign
Dim x: x = 10

' Ternate-like using IIF is NOT available in VBScript, use If instead
If condition Then x = 1 Else x = 0  ' single line if/else
```

## Simple VBS program
Here's a simple **Student Grade Calculator** that covers all the basics:

```vb
Option Explicit

' =============================================
'  STUDENT GRADE CALCULATOR
' =============================================

' -- Variables
Dim studentName, scores(), grade, average
Dim i, total, count

' -- Setup
studentName = "John Doe"
count       = 5

' -- Array (dynamic)
ReDim scores(count - 1)
scores(0) = 85
scores(1) = 90
scores(2) = 78
scores(3) = 92
scores(4) = 88

' =============================================
'  MAIN EXECUTION
' =============================================

total   = CalculateTotal(scores, count)
average = total / count
grade   = GetGrade(average)

PrintReport studentName, scores, average, grade


' =============================================
'  FUNCTIONS & SUBS
' =============================================

Function CalculateTotal(arr, size)
    Dim sum: sum = 0
    For i = 0 To size - 1
        sum = sum + arr(i)
    Next
    CalculateTotal = sum
End Function


Function GetGrade(avg)
    Select Case True
        Case avg >= 90 : GetGrade = "A"
        Case avg >= 80 : GetGrade = "B"
        Case avg >= 70 : GetGrade = "C"
        Case avg >= 60 : GetGrade = "D"
        Case Else      : GetGrade = "F"
    End Select
End Function


Function IsPassing(avg)
    If avg >= 60 Then
        IsPassing = "PASSED"
    Else
        IsPassing = "FAILED"
    End If
End Function


Sub PrintReport(name, arr, avg, gr)
    On Error Resume Next

    Response.Write "<h3>Student Report</h3>"
    Response.Write "<p>Name   : " & name & "</p>"

    ' -- Loop through scores
    Response.Write "<p>Scores : "
    For i = 0 To count - 1
        Response.Write arr(i)
        If i < count - 1 Then Response.Write ", "
    Next
    Response.Write "</p>"

    ' -- String operations
    Dim label: label = "Average Score : " & CStr(Round(avg, 2))
    Response.Write "<p>" & UCase(label) & "</p>"

    ' -- Arithmetic
    Dim highest: highest = arr(0)
    Dim lowest:  lowest  = arr(0)
    For Each score In arr
        If score > highest Then highest = score
        If score < lowest  Then lowest  = score
    Next
    Dim spread: spread = highest - lowest

    Response.Write "<p>Highest : " & highest & "</p>"
    Response.Write "<p>Lowest  : " & lowest  & "</p>"
    Response.Write "<p>Spread  : " & spread  & "</p>"

    ' -- Conditions
    Response.Write "<p>Grade   : " & gr & "</p>"
    Response.Write "<p>Status  : " & IsPassing(avg) & "</p>"

    ' -- Null / Empty check
    If IsEmpty(name) Or IsNull(name) Or Trim(name) = "" Then
        Response.Write "<p style='color:red;'>No student name provided.</p>"
    End If

    If Err.Number <> 0 Then
        Response.Write "<p style='color:red;'>Error: " & Err.Description & "</p>"
        Err.Clear
    End If

    On Error GoTo 0
End Sub
```

**Output would look like:**
```
Student Report
Name    : John Doe
Scores  : 85, 90, 78, 92, 88
AVERAGE SCORE : 86.6
Highest : 92
Lowest  : 78
Spread  : 14
Grade   : B
Status  : PASSED
```

**What it covers:**

| Concept                     | Where Used                                  |
|-----------------------------|---------------------------------------------|
| Variables & Option Explicit | Top of script                               |
| Dynamic Arrays & ReDim      | `scores()`                                  |
| Functions & Subs            | `CalculateTotal`, `GetGrade`, `PrintReport` |
| For / Next & For Each       | Score loop, highest/lowest                  |
| Select Case                 | `GetGrade`                                  |
| If / Else                   | `IsPassing`, name check                     |
| String Operations           | `UCase`, `CStr`, `Trim`                     |
| Arithmetic & Mod            | Total, average, spread                      |
| Null / Empty Checks         | Name validation                             |
| Error Handling              | Inside `PrintReport`                        |

# KEYWORDS, EXPLANATIONS AND FAQs

**1. Opening a Connection**
```vb
Dim conn
Set conn = CreateObject("ADODB.Connection")
conn.Open "Provider=SQLOLEDB;Data Source=SERVER;Initial Catalog=DBNAME;User ID=sa;Password=pass;"
```

**2. Recordset — READ data**
```vb
Dim rst, sql
Set rst = CreateObject("ADODB.Recordset")
sql = "SELECT * FROM Students"
rst.Open sql, conn, 3, 4

Do While Not rst.EOF
    Response.Write rst.Fields("StudentName") & "<br>"
    rst.MoveNext
Loop

rst.Close
Set rst = Nothing
```

**3. Recordset Cursor & Lock Types**
```vb
' rst.Open sql, conn, CursorType, LockType
'
' CursorType:
'   1 = adOpenForwardOnly   (read only, fast, forward only)
'   2 = adOpenKeyset        (read/write, no new records visible)
'   3 = adOpenStatic        (read/write, full scrollable)
'   4 = adOpenDynamic       (read/write, all changes visible)
'
' LockType:
'   1 = adLockReadOnly      (no edits)
'   2 = adLockPessimistic   (locks record on edit)
'   3 = adLockOptimistic    (locks record on update)
'   4 = adLockBatchOptimistic (batch updates)
```

**4. INSERT a Record**
```vb
Dim rstInsert
Set rstInsert = CreateObject("ADODB.Recordset")
rstInsert.Open "SELECT * FROM Students", conn, 3, 4

rstInsert.AddNew
    rstInsert.Fields("StudentID")   = "STU001"
    rstInsert.Fields("StudentName") = "John Doe"
    rstInsert.Fields("Score")       = 88
rstInsert.UpdateBatch

rstInsert.Close
Set rstInsert = Nothing
```

**5. UPDATE a Record**
```vb
Dim rstUpdate
Set rstUpdate = CreateObject("ADODB.Recordset")
rstUpdate.Open "SELECT * FROM Students WHERE StudentID = 'STU001'", conn, 3, 4

If Not rstUpdate.EOF Then
    rstUpdate.Fields("Score") = 95
    rstUpdate.UpdateBatch
End If

rstUpdate.Close
Set rstUpdate = Nothing
```

**6. DELETE a Record**
```vb
Dim rstDelete
Set rstDelete = CreateObject("ADODB.Recordset")
rstDelete.Open "SELECT * FROM Students WHERE StudentID = 'STU001'", conn, 3, 4

If Not rstDelete.EOF Then
    rstDelete.Delete
    rstDelete.UpdateBatch
End If

rstDelete.Close
Set rstDelete = Nothing
```

**7. Execute Direct SQL (no recordset)**
```vb
' Good for INSERT, UPDATE, DELETE when you dont need to read data back
conn.Execute "UPDATE Students SET Score = 95 WHERE StudentID = 'STU001'"
conn.Execute "DELETE FROM Students WHERE StudentID = 'STU001'"
```

**8. Check Record Count**
```vb
rst.Open sql, conn, 3, 4

If rst.RecordCount = 0 Then
    Response.Write "No records found."
ElseIf rst.RecordCount > 0 Then
    Response.Write rst.RecordCount & " records found."
End If
```

**9. Null Field Checks**
```vb
Do While Not rst.EOF
    Dim fieldVal
    fieldVal = ""

    If Not IsNull(rst.Fields("StudentName")) Then
        fieldVal = CStr(Trim(rst.Fields("StudentName")))
    End If

    Response.Write fieldVal & "<br>"
    rst.MoveNext
Loop
```

**10. Error Handling with Database**
```vb
On Error Resume Next
    rst.Open sql, conn, 3, 4
    If Err.Number <> 0 Then
        Response.Write "DB Error: " & Err.Description
        Err.Clear
    End If
On Error GoTo 0
```

**11. Closing Everything Properly**
```vb
' Always close in order: recordset first, then connection
If Not rst Is Nothing Then
    If rst.State = 1 Then rst.Close   ' 1 = adStateOpen
    Set rst = Nothing
End If

If Not conn Is Nothing Then
    If conn.State = 1 Then conn.Close
    Set conn = Nothing
End If
```

**12. Reusable DB Helper Sub**
```vb
Sub SafeClose(rst)
    On Error Resume Next
    If Not rst Is Nothing Then
        If rst.State = 1 Then rst.Close
        Set rst = Nothing
    End If
    On Error GoTo 0
End Sub

' Usage
SafeClose rstInsert
SafeClose rstUpdate
```

---

**Quick Reference**

| Task            | Method                                              |
|-----------------|-----------------------------------------------------|
| Open connection | `conn.Open connectionString`                        |
| Read data       | `rst.Open sql, conn, 3, 4` + loop                   |
| Insert row      | `rst.AddNew` → set fields → `rst.UpdateBatch`       |
| Update row      | open with filter → set fields → `rst.UpdateBatch`   |
| Delete row      | open with filter → `rst.Delete` → `rst.UpdateBatch` |
| Direct SQL      | `conn.Execute sql`                                  |
| Always cleanup  | `rst.Close` → `Set rst = Nothing`                   |