Sub ConsolidateDataFromProtectedFiles()
    Dim folderPath As String
    Dim fileName As String
    Dim workbook As Workbook
    Dim outputWorkbook As Workbook
    Dim outputSheet As Worksheet
    Dim dataSheet As Worksheet
    Dim outputRow As Long
    Dim password As String
    Dim dataRange As Range
    Dim dataArray As Variant
    Dim i As Integer
    
    ' Set the folder path and password
    folderPath = "C:\Path\To\Your\Folder\" ' Update this path
    password = "your_password" ' Update the password

    ' Create a new workbook for consolidated output
    Set outputWorkbook = Workbooks.Add
    Set outputSheet = outputWorkbook.Sheets(1)
    outputSheet.Name = "Consolidated Data"
    
    ' Set header row in the output sheet
    outputSheet.Cells(1, 1).Value = "File Name"
    For i = 1 To 13
        outputSheet.Cells(1, i + 1).Value = "Column " & Chr(64 + i)
    Next i
    
    outputRow = 2 ' Start from the second row to add data

    ' Loop through all Excel files in the folder
    fileName = Dir(folderPath & "*.xls*")
    Do While fileName <> ""
        On Error Resume Next
        ' Open the workbook
        Set workbook = Workbooks.Open(folderPath & fileName, Password:=password)
        On Error GoTo 0
        
        If Not workbook Is Nothing Then
            ' Check if the required sheet exists
            On Error Resume Next
            Set dataSheet = workbook.Sheets("Model level overrides")
            On Error GoTo 0
            
            If Not dataSheet Is Nothing Then
                ' Read data from A2 to M2
                Set dataRange = dataSheet.Range("A2:M2")
                dataArray = dataRange.Value
                
                ' Add data to the consolidated output sheet
                outputSheet.Cells(outputRow, 1).Value = fileName ' Add file name
                For i = 1 To 13
                    outputSheet.Cells(outputRow, i + 1).Value = dataArray(1, i)
                Next i
                
                outputRow = outputRow + 1 ' Move to the next row for the next file
            End If
            
            ' Close the source workbook
            workbook.Close SaveChanges:=False
            Set dataSheet = Nothing
        End If
        
        ' Move to the next file
        fileName = Dir
    Loop

    ' Autofit columns in the output sheet
    outputSheet.Columns.AutoFit

    ' Save the output workbook
    outputWorkbook.SaveAs folderPath & "Consolidated_Data.xlsx"
    outputWorkbook.Close SaveChanges:=True

    MsgBox "Data consolidated successfully!", vbInformation
End Sub
