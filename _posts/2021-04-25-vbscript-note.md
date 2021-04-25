```VB
Class Course
    Private courseName
    Private courseScore

    Public Default Function Constructor(cName, cScore)
        courseName = cName
        courseScore = cScore
        Set Constructor = Me
    End Function

    Public Property Get cName()
        cName = courseName
    End Property

    Public Property Let cName(tempCourseName)
        courseName = tempCourseName
    End Property

    Public Property Get cScore()
        cScore = courseScore
    End Property

    Public Property Let cScore(tempCourseScore)
        courseScore = tempCourseScore
    End Property
End Class

Class Student
    Private studentName
    Private studentID
    Private studentGender
    Private selectCourses(3)
    
    Public Default Function Constructor(sName, sID, sGender, courses())
        Dim len
        studentName = sName
        studentID = sID
        studentGender = sGender
        ' Debug Here
        ' WScript.Echo VarType(courses(0))
        len = UBound(courses)
        For i = 0 To len - 1
            set selectCourses(i) = courses(i)
        Next
        Set Constructor = Me
    End Function

    Public Property Get sName()
        sName = studentName
    End Property

    Public Property Let sName(tempStudentName)
        studentName = tempStudentName
    End Property

    Public Property Get sID()
        sID = studentID
    End Property

    Public Property Let sID(tempStudentID)
        studentID = tempStudentID
    End Property

    Public Property Get sGender()
        sGender = studentGender
    End Property

    Public Property Let sGender(tempStudentGender)
        studentGender = tempStudentGender
    End Property

    Public Property Get sCourses()
        sCourses = selectCourses
    End Property

    Public Sub setCourses(tempCourses())
        Dim len
        len = UBound(tempCourses)
        Set selectCourses = Nothing
        Redim selectCourses(len)
        For i = 0 to len - 1
            set selectCourses(i) = tempCourses(i)
        Next
    End Sub

    Public Property Get averageScore()
        Dim len, sum, count
        sum = 0
        count = 0
        len = UBound(selectCourses)

        For i = 0 to len - 1
            sum = sum + selectCourses(i).cScore
            count = count + 1
        Next

        averageScore = sum / count
    End Property
End Class

Function getColNumInRowByValue(intRow, strValue, objExcel)
    intCol = 1
    intResult = -1
    value = "tmp"
    Do while value <> ""
        value = objExcel.Cells(intRow, intCol).Value
        if value = strValue then
            intResult = intCol
            exit do
        end if
        intCol = intCol + 1    
    Loop
    getColNumInRowByValue = intResult
End Function

Sub Test()
    Dim student(10)
    Dim averScore(10)
    Dim courses(3)
    Dim count
    Dim intRow
    Dim len
    Set objExcel = CreateObject("Excel.Application")
    Set objWorkbook = objExcel.Workbooks.Open("D:\Desktop\test.xlsx")
    objExcel.Worksheets("Sheet1").Activate
    intColOfName = getColNumInRowByValue(1, "Name", objExcel)
    intColOfID = getColNumInRowByValue(1, "ID", objExcel)
    intColOfGender = getColNumInRowByValue(1, "Gender", objExcel)
    intColOfEnglish = getColNumInRowByValue(1, "English", objExcel)
    intColOfChinese = getColNumInRowByValue(1, "Chinese", objExcel)
    intColOfComputer = getColNumInRowByValue(1, "Computer", objExcel)
    intRow = 2
    count = 0
    Do Until objExcel.Cells(intRow, intColOfName).Value = ""
        name = objExcel.Cells(intRow, intColOfName).Value
        id = objExcel.Cells(intRow, intColOfID).Value
        gender = objExcel.Cells(intRow, intColOfGender).Value
        english = objExcel.Cells(intRow, intColOfEnglish).Value
        chinese = objExcel.Cells(intRow, intColOfChinese).Value
        computer = objExcel.Cells(intRow, intColOfComputer).Value
        Set courses(0) = (New Course)("English", CDbl(english))
        Set courses(1) = (New Course)("English", CDbl(chinese))
        Set courses(2) = (New Course)("English", CDbl(computer))
        Set student(count) = (New Student)(name, id, gender, courses)
        ' WScript.Echo student(count).sName
        intRow = intRow + 1
        count = count + 1
    Loop

    objExcel.DisplayAlerts = False
    objWorkbook.Close False

    Set DataList = CreateObject("System.Collections.ArrayList")

    len = UBound(student)
    For i = 0 To len - 1
        If student(i).sGender = "Male" Then
            WScript.Echo "Male Name: " & student(i).sName
        Else
            WScript.Echo "Female ID: " & student(i).sID
        End If
        DataList.Add(student(i).averageScore)
    Next

    DataList.Sort()
    DataList.Reverse()

    For Each scoreItem in DataList
        WScript.Echo "Average Score: " & scoreItem
    Next

End Sub

Test()
```