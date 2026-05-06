# INTRODUCTION
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