---
title: Insert title here
key: 4ac6d7e2ecae5b05febe9a86521c1950

---
## INSTEAD OF Triggers for DML Statements

```yaml
type: "TitleSlide"
key: "ade0eb77bc"
```

`@lower_third`

name: Marta Aghili
title: Delightek Founder


`@script`
Hi, welcome to the course! In this video, you 'll be learning about the reason we use INSTEAD OF triggers   to modify data with DML statements.


---
## INSERT statement for views

```yaml
type: "FullSlide"
key: "f3c3ec2950"
```

`@part1`
| Id | StuName | City      | MajorId |
|----|---------|-----------|---------|
| 1  | Elina   | Toronto   | 3       |
| 2  | Daniel  | Kitchener | 1       |
| 3  | Julia   | Vancouver | 4       |
| 4  | Ryan    | Toronto   | 2       |
| 5  | Emily   | Hamilton  | 1       |
---
| MajorId | MajorName  |
|---------|------------|
| 1       | Psychology |
| 2       | Marketing  |
| 3       | English    |
| 4       | Statisitcs |


`@script`
Assume we have two tables. One named 'Student'has the records of the student names, their cities and major ids.The second table named 'Major'includes the Major ids and major names. 
Now, I want to join these tables to create a view.


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
| 5  | Emily   | Hamilton  | Psychology |


`@script`
I 've used CREATE VIEW statement to join the two tables and make view named 'vwStudent_Major' which has the Student names and their cities from 'Student' table and Major Names from the 'Major' table.


---
## INSERT statement for views

```yaml
type: "FullSlide"
key: "fadf81fb51"
```

`@part1`
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | Psychology |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Hamilton  | Psychology |
---
```r
INSSERT INTO vwStudent_Major VALUES (6, 'Adrian', 'kingston', 'statistics')
``` 
![](https://assets.datacamp.com/production/repositories/4363/datasets/3e649dfaa8e054f29a8783fabe615c6ae5ae8164/ErrorMsg.JPG)


`@script`
If I use INSERT statement to add another row to the view., Since view is the virtual table, the values are going to be added to the two base tables not the view.However,when I run the code SQL will throw an error stating 'View or function 'vwStudent_Major' is not updatable because the modification affects multiple base tables.' Now lets see how to correct the situation using INSTEAD OF triggers.


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
```


`@script`
I 've used the CREATE TRIGGER statement and INSTEAD OF INSERT function. Then, create a variable named 'MajorId' by selecting the 'MajorId' from Major table joining the Insereted table on the 'MajorName' columns from both tables.
Why we join 'Major' table with Inserted table? As we know from the previous course, Inserted table is a special table in SQL that contains the new inserted data. We join them to take the 'MajorName' from Inserted table and retrieve 'MajorId' from 'Major' table. Then, insert that 'MajorId' along with Students' Names and cities into the'Student' table. Also, we need to raise an error to terminate the process if the 'MajorName' is not valid and 'MajorId' related to it would be null. 
Now,if I execute The INSTEAD OF INSERT trigger and INSERT statement the row will be added to the view.


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
As you see, I run the INSERT INTO statement again and the new student 'Adrian' with Id number 6 has been added to the view.


---
## UPDATE statement for views

```yaml
type: "FullSlide"
key: "210b8dd332"
```

`@part1`
```r
UPDATE vwStudent_Major SET City = 'Montreal' , MajorName = 'English' WHERE Id = 2
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | Psychology |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Hamilton  | Psychology |

![](https://assets.datacamp.com/production/repositories/4363/datasets/5c4facf0b314722587fdfd4c6e6b0b009e53436a/UpdateErrorMsg.JPG)


`@script`
What happens if we want to update the view? Let's change Daniel's city and his major name to 'Montreal' and 'English'. In the view, the 'City' column comes from the 'Student' table and 'MajorName' column comes from the 'Major' table. So, like the INSERT statement the update statement is going to affect two underlying tables. If I run the query, I will get the same error as the one I got for INSERT statement.


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
Now, I change the 'MajorName' in the view for Id number 2. In this case, the Update statement will only affect the Major table. So, The UPDATE statement will be executed without error. However, SQL will change all the students in view which has the same major of Id number 2. Here,  Id number 5 is also updated from Psychology to English. To update the view correctly, we need to create INSTEAD OF UPDATE trigger.


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
```


`@script`
In order to update the 'MajorName' for just one student in view, we actually need to change the 'MajorId' in the Student table not to change the 'MajorId' column in Major table. To do that, we declare a variable exactly like the one we create for the INSTEAD OF INSERT trigger. so we first join the Inserted table with the Major table to get the 'MajorId' column from Major table. Then update the 'Student' table joining the '@MajorId' from Inserted table with 'Student' table. to update other columns like 'City' we also join the column 'City' from Inserted table with 'Student' table.


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
If we use DELETE statement to delete a row or rows in the view, we will encounter the same error as INSERT and UPDATE statements. To fix it, we need to use the INSTEAD OF DELETE trigger.


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
---
```r
DELETE FROM vwStudent_Major WHERE Id IN (3,4)
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | Psychology |
| 5  | Emily   | Hamilton  | Psychology |


`@script`
In INSTEAD OF DELETE trigger, we join the Student table with the Deleted table on the deleted ids form Deleted table and Ids in the Student table.


---
## Let's Practice!

```yaml
type: "FinalSlide"
key: "83a5201a7d"
```

`@script`
Now, it's time to do some actual coding to learn the INSTEAD OF triggers better.

