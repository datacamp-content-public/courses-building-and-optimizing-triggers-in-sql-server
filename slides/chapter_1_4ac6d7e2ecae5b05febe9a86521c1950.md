---
title: Insert title here
key: 4ac6d7e2ecae5b05febe9a86521c1950

---
## INSTEAD OF Triggers vs DML Statements

```yaml
type: "TitleSlide"
key: "ade0eb77bc"
```

`@lower_third`

name: Marta Aghili
title: Delightek Founder


`@script`
Hi, in this chapter, you will learn about the situations we use INSTEAD OF triggers to modify data with DML statements.


---
## INSERT statement for views

```yaml
type: "FullSlide"
key: "f3c3ec2950"
```

`@part1`
Student

| Id | StuName | City      | MajorId |
|----|---------|-----------|---------|
| 1  | Elina   | Toronto   | 3       |
| 2  | Daniel  | Kitchener | 1       |
| 3  | Julia   | Vancouver | 4       |
| 4  | Ryan    | Toronto   | 2       |
| 5  | Emily   | Hamilton  | 1       |


`@script`
Assume we have two tables; One named 'Student'has the records of the students' names, their cities and major ids.


---
## INSERT statement for views

```yaml
type: "FullSlide"
key: "58b6ae5a7e"
```

`@part1`
Major

| MajorId | MajorName  |
|---------|------------|
| 1       | Psychology |
| 2       | Marketing  |
| 3       | English    |
| 4       | Statistics |


`@script`
The second table named 'Major' includes the Major ids and major names. Now, I want to join these two tables to create a view.


---
## INSERT statement for views

```yaml
type: "FullSlide"
key: "b50f11ca8d"
```

`@part1`
```r
CREATE VIEW vwStudent_Major
 AS
 SELECT Id, StuName, City, MajorName from Student 
 JOIN Major 
 ON Student.MajorId = Major.MajorId
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | Psychology |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Hamilton  | Psychology |{{1}}


`@script`
I 've used CREATE VIEW statement to join the tables and make a view. This view named 'vwStudent_Major' has the Student names and their cities from the 'Student' table and Major Names from the 'Major' table.


---
## INSERT statement for views

```yaml
type: "FullSlide"
key: "92998048b9"
```

`@part1`
```r
INSERT INTO vwStudent_Major VALUES (6, 'Adrian', 'kingston', 'statistics') 
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | Psychology |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Hamilton  | Psychology |

![](https://assets.datacamp.com/production/repositories/4363/datasets/3e649dfaa8e054f29a8783fabe615c6ae5ae8164/ErrorMsg.JPG)


`@script`
Let's add another row to the view by using INSERT statement. When I run the code, SQL will throw an error stating 'View or function 'vwStudent_Major' is not updatable because the modification affects multiple base tables.' The reason for the error message is that the values should be added to the two base tables not the view. Because, views are virtual tables, the INSERT statement cannot insert any rows to the tables. Let's create INSTEAD OF INSERT trigger to solve the problem.


---
## INSTEAD OF INSERT trigger

```yaml
type: "FullSlide"
key: "d605749c88"
```

`@part1`
```r
CREATE TRIGGER tr_vwStudent_Major_Insert
 ON vwStudent_Major
 INSTEAD OF INSERT
 AS
 BEGIN
	DECLARE @MajorId INT
	SELECT @MajorId = MajorId
    FROM Major
	JOIN Inserted
	ON Inserted.MajorName = Major.MajorName
	
    IF (@MajorId is null)
	BEGIN
	RAISERROR ('The MajorName is invalid',16, 1)
	RETURN
END
	 INSERT INTO Student(Id, StuName, City, MajorId)
	 SELECT Id, StuName, City, @MajorId
	 FROM Inserted
END
```{{1}}


`@script`
In the CREATE TRIGGER statement, I add INSTEAD OF INSERT and create a variable named 'MajorId' by joining the 'MajorId' from 'Major' table with the 'MajorId' from Inserted table.
As we know from the previous course, Inserted table is a special table in SQL that contains the new inserted data. We join them to take the 'MajorName' from Inserted table and retrieve 'MajorId' from 'Major' table. Then, insert that 'MajorId' along with Students' Names and cities into the'Student' table. Also, we need to raise an error to terminate the process if the 'MajorName' is not valid and doesn't have a 'MajorId' related to it.


---
## INSTEAD OF INSERT trigger

```yaml
type: "FullSlide"
key: "de2958e419"
```

`@part1`
```r
INSERT INTO vwStudent_Major VALUES (6, 'Adrian', 'Kingston', 'Statistics')
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | Psychology |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Hamilton  | Psychology |
| 6  | Adrian  | Kingston  | Statistics |


`@script`
After executing the trigger, I run the INSERT statement again and the new student 'Adrian' with Id number 6 will be added to the view.


---
## UPDATE statement for views

```yaml
type: "FullSlide"
key: "001cca68f6"
```

`@part1`
```r
UPDATE vwStudent_Major SET MajorName = 'English' where Id = 2
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | English    |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Hamilton  | English    |


`@script`
Now, let's see what happens if I update the 'MajorName' from 'Psychology' to 'English' for Id number 2? In this case, the Update statement will only affect the underlying Major table and will be executed without error. But, SQL will change the major names of all the students which are the same as Id number 2. For example, You see that the 'MajorName' of  Id number 5 is also updated from 'Psychology' to 'English'. We can correct the situation by creating INSTEAD OF UPDATE trigger.


---
## INSTEAD OF UPDATE trigger

```yaml
type: "FullSlide"
key: "59a5d80ffc"
```

`@part1`
```r
CREATE TRIGGER tr_vwStudent_Major_Update
 on vwStudent_Major
 INSTEAD OF UPDATE
 AS 
 BEGIN
      IF (UPDATE(MajorName))
      BEGIN
	        DECLARE @MajorId INT
	        SELECT @MajorId = MajorId FROM Major
	        JOIN Inserted
	        ON Inserted.MajorName = Major.MajorName
		
	        UPDATE Student SET MajorId = @MajorId FROM Inserted 
	        JOIN Student
	        ON Student.Id = Inserted.Id
     END
     IF (UPDATE(City))
     BEGIN
	        UPDATE Student SET City = Inserted.City
	        FROM Inserted 
		    JOIN Student
			ON Student.Id = Inserted.Id
	END
END
```{{1}}


`@script`
To update the 'MajorName' for just one student in the view, we need to create INSTEAD OF UPDATE trigger. Then, declare a variable exactly like the one we created for the INSTEAD OF INSERT trigger.  To update other columns like 'City' we also join the 'City' column from Inserted table with the one from the 'Student' table.


---
## INSTEAD OF UPDATE trigger

```yaml
type: "FullSlide"
key: "a29e1db042"
```

`@part1`
```r
UPDATE vwStudent_Major SET MajorName = 'English' where Id = 2
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | English    |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Hamilton  | Psychology |


`@script`
I execute the UPDATE statement again and this time only the 'MajorName' in the second row is changed from 'Psychology' to ''English.


---
## DELETE Statement for views

```yaml
type: "FullSlide"
key: "25ed28ea63"
```

`@part1`
```r
DELETE FROM vwStudent_Major WHERE Id in (3,4)
```
![](https://assets.datacamp.com/production/repositories/4363/datasets/ef81984fad1a5c62950b94b8f65e222cb54dc409/DeleteErrorMsg.JPG)


`@script`
If we use DELETE statement to delete a row or rows in the view, we will encounter the same error as INSERT statement. To fix it, we need to use the INSTEAD OF DELETE trigger.


---
## INSTEAD OF DELETE trigger

```yaml
type: "FullSlide"
key: "5363b609d6"
```

`@part1`
```r
CREATE TRIGGER tr_vwStudent_Major_Delete
ON vwStudent_Major
INSTEAD OF DELETE
AS
BEGIN
	DELETE Student
	FROM Student
	JOIN Deleted
	ON Student.Id = Deleted. Id
END
```
```r
DELETE FROM vwStudent_Major WHERE Id in (3,4)
```
| Id | StuName | City      | MajorName    |
|----|-----------|-----------|------------|
| 1  | Elina     | Toronto   | English    |
| 2  | Daniel    | Kitchener | Psychology |
| 5  | Emily     | Hamilton  | Psychology |{{1}}


`@script`
In INSTEAD OF DELETE trigger, we join the 'Student' table with the Deleted table. The Deleted table is a SQL special table and contains the deleted data. After executing the trigger, I can run the DELETE statement to delete rows with Id numbers 3 and 4.


---
## Let's Practice!

```yaml
type: "FinalSlide"
key: "83a5201a7d"
```

`@script`
Now, it's time to do some actual coding to learn the INSTEAD OF triggers better.

