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



`@script`



---
## Let's Practice!

```yaml
type: "FinalSlide"
key: "83a5201a7d"
```

`@script`
Now, it's time to do some actual coding to learn the INSTEAD OF triggers better.

