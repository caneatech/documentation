# Database Connection Manual
*Classic ASP / VBScript — ADO Framework*

---

## 1. The Connection Object (`conn`)

The framework uses a global `ADODB.Connection` called `conn`. It is opened once at startup in a shared include file and reused by every Sub.

```vb
' Typically in conn.asp (included on every page)
Dim conn
Set conn = CreateObject("ADODB.Connection")
conn.Open "Provider=SQLOLEDB;Data Source=SERVER;" & _
          "Initial Catalog=DB_NAME;User ID=sa;Password=pass;"
```

> **Note:** Never re-create `conn` inside a Sub. Always use the shared instance.

---

## 2. Declaring Recordsets

Each Sub creates its own local Recordset variables. Multiple can coexist.

```vb
Dim rst, rst2, rst3
Set rst  = CreateObject("ADODB.Recordset")
Set rst2 = CreateObject("ADODB.Recordset")
Set rst3 = CreateObject("ADODB.Recordset")
```

---

## 3. Opening a Query

Build a SQL string then call `.Open` with `conn`. This framework always uses cursor type `3` (adOpenStatic) and lock type `4` (adLockBatchOptimistic).

```vb
Dim sql
sql = "SELECT * FROM visitation"
sql = sql & " WHERE visitdate2 BETWEEN '" & dat1 & "' AND '" & dat2 & "'"

With rst2
    .Open sql, conn, 3, 4
    ...
End With
```

---

## 4. Reading Records

Check `RecordCount > 0` before looping. Call `.MoveFirst`, then loop with `.MoveNext` until `.EOF`.

```vb
If .RecordCount > 0 Then
    .MoveFirst
    Do While Not .EOF
        Dim patId : patId = .Fields("Patientid")
        Dim grp   : grp   = .Fields("insuranceSchemeid")
        .MoveNext
    Loop
End If
```

---

## 5. Closing & Cleanup

Close the Recordset inside the `With` block. Release all objects with `Set = Nothing` at the end of the Sub.

```vb
.Close          ' Inside the With block

' At the end of the Sub:
Set rst  = Nothing
Set rst2 = Nothing
Set rst3 = Nothing
```

> **Note:** Skipping cleanup causes memory leaks in Classic ASP.

---

## 6. Full Minimal Example

```vb
Sub ShowPatients(dat1, dat2)
    Dim rst, sql
    Set rst = CreateObject("ADODB.Recordset")

    sql = "SELECT * FROM visitation"
    sql = sql & " WHERE visitdate2 BETWEEN '" & dat1 & "' AND '" & dat2 & "'"
    sql = sql & " AND specialistTypeid = 'S012' ORDER BY visitdate2"

    With rst
        .Open sql, conn, 3, 4
        If .RecordCount > 0 Then
            .MoveFirst
            Do While Not .EOF
                response.write .Fields("visitationname") & "<br>"
                .MoveNext
            Loop
        End If
        .Close
    End With

    Set rst = Nothing
End Sub
```

---

## 7. Quick Reference

| Method / Property | Description |
|---|---|
| `CreateObject("ADODB.Recordset")` | Create a recordset |
| `.Open sql, conn, 3, 4` | Run query (cursorType=3, lockType=4) |
| `.RecordCount` | Number of rows returned |
| `.MoveFirst` | Go to first record |
| `.MoveNext` | Advance to next record |
| `.EOF` | True when past last record |
| `.Fields("ColumnName")` | Read a column value |
| `.Close` | Close the recordset |
| `Set rst = Nothing` | Release from memory |

---

*Lighthouse Hospital — Internal Framework Reference*