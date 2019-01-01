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
Hi, welcome to the course! In this video, you 'll be learning about the reason we use INSTEAD OF triggers   to modify data with DML statements and what happens if we don't use them.


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
| 5  | Emily   | Manitoba  | 1       |
---
| MajorId | MajorName  |
|---------|------------|
| 1       | Psychology |
| 2       | Marketing  |
| 3       | English    |
| 4       | Statisitcs |


`@script`
Assume we have two tables one named 'Student' is the records of the student names, their cities and major ids.The second table named 'Major' holding the Major ids  and major names. 
Now, I want to join these tables to create a view.


---
## INSERT statement for views

```yaml
type: "FullSlide"
key: "b50f11ca8d"
```

`@part1`
```r
Create view vwStudent_Major
 as
 select Id, StuName, City, MajorName from Student as S
 join Major as M
 on S.MajorId = M.MajorId
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | Psychology |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Manitoba  | Psychology |


`@script`
I have used CREATE VIEW statement to join the two tables and make view named'vwStudent_Major' which has the Student names and their cities from 'Student' table and Major Names from the 'Major' table.


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
| 5  | Emily   | Manitoba  | Psychology |
---
```r
Insert into vwStudent_Major values (6, 'Adrian', 'kingston', 'statistics')
``` 
![](https://assets.datacamp.com/production/repositories/4363/datasets/3e649dfaa8e054f29a8783fabe615c6ae5ae8164/ErrorMsg.JPG)


`@script`
Now if I use INSERT statement to add another row to the view., Since view is the virtual table, the values we are inserting as the new student should be going to be added to the two base tables.However,when I run the code SQL will throw an error stating 'View or function 'vwStudent_Major' is not updatable bacause the modification affects multiple base tables.'Now lets see how to correct the situation using INSREAD OF triggers.


---
## INSTEAD OF INSERT trigger

```yaml
type: "FullSlide"
key: "d605749c88"
```

`@part1`
```r
CREATE TRIGGER tr_vwStudent_Major_Insert
 on vwStudent_Major
 instead of insert
 as 
 begin
	declare @MajorId int
	select @MajorId = MajorId
	 from major
	 join Inserted
	 on Inserted.MajorName = Major.MajorName
	 if (@MajorId is null)
	 begin
	 raiserror ('The MajorName is invalid',16, 1)
	 return
End
	 Insert into Student(Id, StuName, City, MajorId)
	 select Id, StuName, City, @MajorId
	 from Inserted
 End
```


`@script`
as you see, in the CREATE TRIGGER statement we create a variable named MajorId and select the MajorId from MAjor table joining the Insereted table on the MajorName. 
As we know from the previous course, Inserted table is a special table in SQL that contains the new data. Why we do that? because take the MajorName from Inserted table and retrieve MajorId from Major table. Then, insert that MajorId along with student‘s name and city into Student table. Also, we need to raise an error to terminate the process if the MajorName is not valid and MajorId related to it would be null. 
Now,if I execute the INSERT statement again the row will be added to the view.


---
## INSTEAD OF INSERT trigger

```yaml
type: "FullSlide"
key: "de2958e419"
```

`@part1`
```r
Insert into vwStudent_Major values (6, 'Adrian', 'kingston', 'statistics')
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | Psychology |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Manitoba  | Psychology |
| 6  | Adrian  | London    | Statistics |


`@script`



---
## UPDATE statement for views

```yaml
type: "FullSlide"
key: "210b8dd332"
```

`@part1`
```r
update vwStudent_Major set City = 'Montreal' , MajorName = 'English' where Id = 2
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | Psychology |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Manitoba  | Psychology |
| 6  | Adrian  | London    | Statistics |
![](https://assets.datacamp.com/production/repositories/4363/datasets/5c4facf0b314722587fdfd4c6e6b0b009e53436a/UpdateErrorMsg.JPG)


`@script`
What happens if we want to update the view? Let's change Daniel's city and major name to 'Montreal' and 'English'. The City column comes from the Student table and MajorName column comes from the Major table. So, like the INSERT statement the update statement is going to affect two underlying tables. If I run the query, I will get the same error.


---
## UPDATE statement for views

```yaml
type: "FullSlide"
key: "001cca68f6"
```

`@part1`
```r
update vwStudent_Major set MajorName = 'English' where Id = 2
```
| Id | StuName | City      | MajorName  |
|----|---------|-----------|------------|
| 1  | Elina   | Toronto   | English    |
| 2  | Daniel  | Kitchener | English |
| 3  | Julia   | Vancouver | Statistics |
| 4  | Ryan    | Toronto   | Marketing  |
| 5  | Emily   | Manitoba  | English |
| 6  | Adrian  | London    | Statistics |


`@script`
Now, I change the MajorName in the view for Id number 2. So, the Update statement will only affect the Major table. The UPDATE statement will be executed without error because, the Update statement will only affect the Major table.However, SQL will change all the students in view which has the same major of Id number 2. In this case Id number 5 is also updated from Psychology to English.


---
## Let's Practice!

```yaml
type: "FinalSlide"
key: "83a5201a7d"
```

`@script`
Now, it's time to do some actual coding to learn the INSTEAD OF triggers better.

