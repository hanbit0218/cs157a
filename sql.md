# SQL Summary, Notes, and Practice Problems

## 1. SQL Overview

SQL (Structured Query Language) is used for querying and managing relational databases. It consists of:

- DML (Data Manipulation Language)
- DDL (Data Definition Language)
- DCL (Data Control Language)

## 2. Creating Database Schemas (DDL)

### Creating Tables

```sql
CREATE TABLE <name> (
  <list of elements>
);
```

### Deleting Tables

```sql
DROP TABLE <name>;
```

### Declaring Keys

Single-attribute key:

```sql
CREATE TABLE Beers (
  name CHAR(20) UNIQUE,
  manf CHAR(20)
);
```

Multi-attribute key:

```sql
CREATE TABLE Sells (
  bar CHAR(20),
  beer VARCHAR(20),
  price REAL,
  PRIMARY KEY (bar, beer)
);
```

### PRIMARY KEY vs. UNIQUE

- Only one PRIMARY KEY per relation, multiple UNIQUE attributes allowed.
- PRIMARY KEY cannot contain NULL values, UNIQUE can.

## 3. SQL Queries (DML)

### Basic Structure

```sql
SELECT desired attributes
FROM one or more tables
WHERE condition about tuples of the tables
```

### Key Features

- Use `*` to select all attributes
- Rename attributes with `AS`
- Use expressions in SELECT clauses
- Complex conditions in WHERE clause using AND, OR, NOT, and comparison operators
- Pattern matching with LIKE and wildcards (% and \_)
- NULL values and three-valued logic (TRUE, FALSE, UNKNOWN)
- Multi-table queries using comma-separated table names in FROM clause
- Self-joins using table aliases

## 4. Subqueries

- Can be used in FROM and WHERE clauses
- Single-tuple subqueries
- IN and EXISTS operators
- Set operations: UNION, INTERSECT, EXCEPT
- Use DISTINCT to remove duplicates

## Practice Problems

### Problem 1

Given the schema:

```sql
Employees(id, name, department, salary)
Departments(id, name, budget)
Projects(id, name, department_id)
Assignments(employee_id, project_id, hours)
```

Find the names of employees who work on all projects in their department.

### Problem 2

Using the same schema, find the department with the highest average salary, but only consider departments with more than 5 employees.

### Problem 3

Create a table schema for a university database that includes students, courses, professors, and enrollments. Include appropriate primary and foreign keys, and at least one multi-attribute key.

### Problem 4

Find pairs of employees who have worked on the same project for more than 100 hours each. Order the results by the sum of their hours in descending order.

### Problem 5

Create a query that returns a result set with three columns: department name, number of employees, and a category (Small: <10 employees, Medium: 10-50 employees, Large: >50 employees).

## Answer Sheet

### Answer 1

```sql
SELECT DISTINCT e.name
FROM Employees e
WHERE NOT EXISTS (
  SELECT p.id
  FROM Projects p
  WHERE p.department_id = e.department
  AND NOT EXISTS (
    SELECT *
    FROM Assignments a
    WHERE a.employee_id = e.id AND a.project_id = p.id
  )
);
```

### Answer 2

```sql
SELECT d.name, AVG(e.salary) as avg_salary
FROM Departments d
JOIN Employees e ON d.id = e.department
GROUP BY d.id, d.name
HAVING COUNT(e.id) > 5
ORDER BY avg_salary DESC
LIMIT 1;
```

### Answer 3

```sql
CREATE TABLE Students (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100) UNIQUE,
  date_of_birth DATE
);

CREATE TABLE Professors (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  department VARCHAR(50),
  email VARCHAR(100) UNIQUE
);

CREATE TABLE Courses (
  id VARCHAR(10) PRIMARY KEY,
  name VARCHAR(100),
  credits INT,
  professor_id INT,
  FOREIGN KEY (professor_id) REFERENCES Professors(id)
);

CREATE TABLE Enrollments (
  student_id INT,
  course_id VARCHAR(10),
  semester VARCHAR(20),
  grade CHAR(2),
  PRIMARY KEY (student_id, course_id, semester),
  FOREIGN KEY (student_id) REFERENCES Students(id),
  FOREIGN KEY (course_id) REFERENCES Courses(id)
);
```

### Answer 4

```sql
SELECT e1.name AS employee1, e2.name AS employee2,
       a1.project_id, (a1.hours + a2.hours) AS total_hours
FROM Assignments a1
JOIN Assignments a2 ON a1.project_id = a2.project_id
JOIN Employees e1 ON a1.employee_id = e1.id
JOIN Employees e2 ON a2.employee_id = e2.id
WHERE a1.employee_id < a2.employee_id
  AND a1.hours > 100 AND a2.hours > 100
ORDER BY total_hours DESC;
```

### Answer 5

```sql
SELECT d.name AS department_name,
       COUNT(e.id) AS num_employees,
       CASE
         WHEN COUNT(e.id) < 10 THEN 'Small'
         WHEN COUNT(e.id) BETWEEN 10 AND 50 THEN 'Medium'
         ELSE 'Large'
       END AS category
FROM Departments d
LEFT JOIN Employees e ON d.id = e.department
GROUP BY d.id, d.name
ORDER BY num_employees DESC;
```
