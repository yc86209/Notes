# 18. Customers Who Never Order

link

## Description

## Solution

```SQL
/* Write your T-SQL query statement below */

```

## SQL Schema

```SQL
DROP TABLE IF EXISTS Person;

```



# 262. Trips and Users

https://leetcode.com/problems/trips-and-users/

## Description

The `Trips` table holds all taxi trips. Each trip has a unique Id, while Client_Id and Driver_Id are both foreign keys to the Users_Id at the `Users` table. Status is an ENUM type of (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’).

```
+----+-----------+-----------+---------+--------------------+----------+
| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
+----+-----------+-----------+---------+--------------------+----------+
| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|
+----+-----------+-----------+---------+--------------------+----------+
```

The `Users` table holds all users. Each user has an unique Users_Id, and Role is an ENUM type of (‘client’, ‘driver’, ‘partner’).

```
+----------+--------+--------+
| Users_Id | Banned |  Role  |
+----------+--------+--------+
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |
+----------+--------+--------+
```

Write a SQL query to find the cancellation rate of requests made by unbanned users (both client and driver must be unbanned) between **Oct 1, 2013** and **Oct 3, 2013**. The cancellation rate is computed by dividing the number of canceled (by client or driver) requests made by unbanned users by the total number of requests made by unbanned users.

For the above tables, your SQL query should return the following rows with the cancellation rate being rounded to *two* decimal places.

```
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+
```

## Solution

```SQL
/* Write your T-SQL query statement below */
SELECT A.Day,CAST((ISNULL(B.FailTotal,0)+0.0)/(A.Total+0.0) AS NUMERIC(10,2)) as [Cancellation Rate] FROM (
	SELECT Request_at as Day,Count(*) as Total from(
		SELECT * FROM Trips A
		left join Users B on B.Banned='NO' and Role='client' and A.Client_Id=B.Users_Id
		where B.Users_Id is not null and Request_at between '2013-10-01' and '2013-10-03'
	) A GROUP BY Request_at
) A
left join (
	SELECT Request_at as Day,Count(*) as FailTotal from(
		SELECT * FROM Trips A
		left join Users B on B.Banned='NO' and Role='client' and A.Client_Id=B.Users_Id
	where B.Users_Id is not null and A.Status<>'completed' and Request_at between '2013-10-01' and '2013-10-03'
	) A GROUP BY A.Request_at
)  B on A.Day=B.Day
```

## SQL Schema

```SQL
DROP TABLE IF EXISTS Trips;
Create table Trips (Id int, Client_Id int, Driver_Id int, City_Id int,Status VARCHAR(30) NOT NULL CHECK (Status IN('completed', 'cancelled_by_driver', 'cancelled_by_client')), Request_at varchar(50))
DROP TABLE IF EXISTS Users;
Create table Users (Users_Id int, Banned varchar(50),Role VARCHAR(30) NOT NULL CHECK (Role IN('client', 'driver', 'partner')))
Truncate table Trips
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('1', '1', '10', '1', 'completed', '2013-10-01')
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('2', '2', '11', '1', 'cancelled_by_driver', '2013-10-01')
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('3', '3', '12', '6', 'completed', '2013-10-01')
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('4', '4', '13', '6', 'cancelled_by_client', '2013-10-01')
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('5', '1', '10', '1', 'completed', '2013-10-02')
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('6', '2', '11', '6', 'completed', '2013-10-02')
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('7', '3', '12', '6', 'completed', '2013-10-02')
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('8', '2', '12', '12', 'completed', '2013-10-03')
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('9', '3', '10', '12', 'completed', '2013-10-03')
insert into Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) values ('10', '4', '13', '12', 'cancelled_by_driver', '2013-10-03')
Truncate table Users
insert into Users (Users_Id, Banned, Role) values ('1', 'No', 'client')
insert into Users (Users_Id, Banned, Role) values ('2', 'Yes', 'client')
insert into Users (Users_Id, Banned, Role) values ('3', 'No', 'client')
insert into Users (Users_Id, Banned, Role) values ('4', 'No', 'client')
insert into Users (Users_Id, Banned, Role) values ('10', 'No', 'driver')
insert into Users (Users_Id, Banned, Role) values ('11', 'No', 'driver')
insert into Users (Users_Id, Banned, Role) values ('12', 'No', 'driver')
insert into Users (Users_Id, Banned, Role) values ('13', 'No', 'driver')
```

# 197. Rising Temperature

https://leetcode.com/problems/rising-temperature/

## Description

Given a `Weather` table, write a SQL query to find all dates' Ids with higher temperature compared to its previous (yesterday's) dates.

```
+---------+------------------+------------------+
| Id(INT) | RecordDate(DATE) | Temperature(INT) |
+---------+------------------+------------------+
|       1 |       2015-01-01 |               10 |
|       2 |       2015-01-02 |               25 |
|       3 |       2015-01-03 |               20 |
|       4 |       2015-01-04 |               30 |
+---------+------------------+------------------+
```

For example, return the following Ids for the above `Weather` table:

```
+----+
| Id |
+----+
|  2 |
|  4 |
+----+
```

## Solution

```SQL
/* Write your T-SQL query statement below */

select A.Id from Weather A
left join Weather B on convert(datetime, A.RecordDate, 105)-1=convert(datetime, B.RecordDate, 105)
where B.Id is not null and A.Temperature>B.Temperature
```

## SQL Schema

```SQL
DROP TABLE IF EXISTS Weather;
Create table Weather (Id int, RecordDate date, Temperature int)
Truncate table Weather
insert into Weather (Id, RecordDate, Temperature) values ('1', '2015-01-01', '10')
insert into Weather (Id, RecordDate, Temperature) values ('2', '2015-01-02', '25')
insert into Weather (Id, RecordDate, Temperature) values ('3', '2015-01-03', '20')
insert into Weather (Id, RecordDate, Temperature) values ('4', '2015-01-04', '30')
```

# 196. Delete Duplicate Emails

https://leetcode.com/problems/delete-duplicate-emails/

## Description

Write a SQL query to **delete** all duplicate email entries in a table named `Person`, keeping only unique emails based on its *smallest* **Id**.

```
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
Id is the primary key column for this table.
```

For example, after running your query, the above `Person` table should have the following rows:

```
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```

**Note:**

Your output is the whole `Person` table after executing your sql. Use `delete` statement.

## Solution

```SQL
/* Write your T-SQL query statement below */

DELETE A FROM Person A
left join (
	select Id,Email,DENSE_RANK() over(partition by Email order by Id) as rowId
	from Person
) B on A.Id=B.Id
where B.rowId<>1
```

## SQL Schema

```SQL
DROP TABLE IF EXISTS Person;
Create table Person (Id int, Email varchar(255))
Truncate table Person
insert into Person (Id, Email) values ('1', 'a@b.com')
insert into Person (Id, Email) values ('2', 'c@d.com')
insert into Person (Id, Email) values ('3', 'a@b.com')
```

# 185. Department Top Three Salaries

https://leetcode.com/problems/department-top-three-salaries/

## Description

The `Employee` table holds all employees. Every employee has an Id, and there is also a column for the department Id.

```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |
+----+-------+--------+--------------+
```

The `Department` table holds all departments of the company.

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

Write a SQL query to find employees who earn the top three salaries in each of the department. For the above tables, your SQL query should return the following rows (order of rows does not matter).

```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
```

## Solution

```SQL
/* Write your T-SQL query statement below */
select Department,Employee,Salary from (
	select A.Name Employee,B.Name Department,Salary,DENSE_RANK() over(partition by B.Id order by Salary DESC) as row
	from Employee A
	left join Department B on A.DepartmentId=B.Id
	where B.id is not null
) A
where row<=3
```

## SQL Schema

```SQL
DROP TABLE IF EXISTS Employee;
Create table Employee (Id int, Name varchar(255), Salary int, DepartmentId int)
DROP TABLE IF EXISTS Department;
Create table Department (Id int, Name varchar(255))
Truncate table Employee
insert into Employee (Id, Name, Salary, DepartmentId) values ('1', 'Joe', '85000', '1')
insert into Employee (Id, Name, Salary, DepartmentId) values ('2', 'Henry', '80000', '2')
insert into Employee (Id, Name, Salary, DepartmentId) values ('3', 'Sam', '60000', '2')
insert into Employee (Id, Name, Salary, DepartmentId) values ('4', 'Max', '90000', '1')
insert into Employee (Id, Name, Salary, DepartmentId) values ('5', 'Janet', '69000', '1')
insert into Employee (Id, Name, Salary, DepartmentId) values ('6', 'Randy', '85000', '1')
insert into Employee (Id, Name, Salary, DepartmentId) values ('7', 'Will', '70000', '1')
Truncate table Department
insert into Department (Id, Name) values ('1', 'IT')
insert into Department (Id, Name) values ('2', 'Sales')
```

# 184. Department Highest Salary

https://leetcode.com/problems/department-highest-salary/

## Description

The `Employee` table holds all employees. Every employee has an Id, a salary, and there is also a column for the department Id.

```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
```

The `Department` table holds all departments of the company.

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

Write a SQL query to find employees who have the highest salary in each of the departments. For the above tables, your SQL query should return the following rows (order of rows does not matter).

```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```

## Solution

```SQL
/* Write your T-SQL query statement below */
select Department,Employee,Salary from (
	select A.Name Employee,B.Name Department,Salary,DENSE_RANK() over(partition by B.Id order by Salary DESC) as row
	from Employee A
	left join Department B on A.DepartmentId=B.Id
	where B.id is not null
) A
where row=1
```

## SQL Schema

```SQL
DROP TABLE IF EXISTS Employee;
Create table Employee (Id int, Name varchar(255), Salary int, DepartmentId int)
DROP TABLE IF EXISTS Department;
Create table Department (Id int, Name varchar(255))
Truncate table Employee
insert into Employee (Id, Name, Salary, DepartmentId) values ('1', 'Joe', '70000', '1')
insert into Employee (Id, Name, Salary, DepartmentId) values ('2', 'Jim', '90000', '1')
insert into Employee (Id, Name, Salary, DepartmentId) values ('3', 'Henry', '80000', '2')
insert into Employee (Id, Name, Salary, DepartmentId) values ('4', 'Sam', '60000', '2')
insert into Employee (Id, Name, Salary, DepartmentId) values ('5', 'Max', '90000', '1')
Truncate table Department
insert into Department (Id, Name) values ('1', 'IT')
insert into Department (Id, Name) values ('2', 'Sales')
```

# 183. Customers Who Never Order

https://leetcode.com/problems/customers-who-never-order/

## Description

Table: `Customers`.

```
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
```

Table: `Orders`.

```
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

Using the above tables as example, return the following:

```
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

## Solution

```SQL
/* Write your T-SQL query statement below */
select Name as Customers  
from Customers A
left join Orders B on A.Id=B.CustomerId
where B.Id is null
```

## SQL Schema

```SQL
DROP TABLE IF EXISTS Customers
Create table Customers (Id int, Name varchar(255))
DROP TABLE IF EXISTS Orders
Create table Orders (Id int, CustomerId int)
Truncate table Customers
insert into Customers (Id, Name) values ('1', 'Joe')
insert into Customers (Id, Name) values ('2', 'Henry')
insert into Customers (Id, Name) values ('3', 'Sam')
insert into Customers (Id, Name) values ('4', 'Max')
Truncate table Orders
insert into Orders (Id, CustomerId) values ('1', '3')
insert into Orders (Id, CustomerId) values ('2', '1')
```

# 182. Duplicate Emails

https://leetcode.com/problems/duplicate-emails/

## Description

Write a SQL query to find all duplicate emails in a table named `Person`.

```
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```

For example, your query should return the following for the above table:

```
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```

**Note**: All emails are in lowercase.

## Solution

```SQL
SELECT TOP 100 Email FROM Person WITH(NOLOCK)
GROUP BY Email
HAVING COUNT(Email)>1
```

## SQL Schema

```SQL
DROP TABLE IF EXISTS Person;
Create table Person (Id int, Email varchar(255))
Truncate table Person
insert into Person (Id, Email) values ('1', 'a@b.com')
insert into Person (Id, Email) values ('2', 'c@d.com')
insert into Person (Id, Email) values ('3', 'a@b.com')
```

# 175. Combine Two Tables

https://leetcode.com/problems/combine-two-tables/

## Description

## Solution

```SQL
/* Write your T-SQL query statement below */

```

## SQL Schema

```SQL
DROP TABLE IF EXISTS Person;

```

