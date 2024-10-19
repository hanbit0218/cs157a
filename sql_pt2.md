# SQL Notes Summary

To create a downloadable file:
1. Copy all the content below this line.
2. Open a text editor (like Notepad, TextEdit, or VS Code).
3. Paste the copied content into the text editor.
4. Save the file with a .md extension (e.g., "SQL_Notes_Summary.md").

---

# SQL Notes Summary

## 1. JOIN Operators

### Inner Join
- Cross Join (X): Cartesian product
- Natural Join (⋈): Cross join with union of attributes
- Theta Join (⋈C): Natural join with boolean condition

### Outer Join
- Left Outer Join (⟕): All left tuples, matching right tuples
- Right Outer Join (⟖): All right tuples, matching left tuples
- Full Outer Join (⟗): Union of left and right joins

### Self Join
- Joining a table to itself

## 2. JOIN Details

- Inner Join Syntax: `R INNER JOIN S`
- Outer Join Syntax: `R OUTER JOIN S`
- Can be modified with `NATURAL`, `ON <condition>`, or `LEFT/RIGHT/FULL`
- Multiple table joins possible

## 3. Aggregations

- Functions: `SUM`, `AVG`, `COUNT`, `MIN`, `MAX`
- Use `DISTINCT` to eliminate duplicates
- NULLs ignored in aggregations

## 4. GROUP BY and HAVING

- `GROUP BY`: Groups rows based on specified attributes
- `HAVING`: Filters groups after `GROUP BY`
- SELECT list restrictions with `GROUP BY`

## 5. Database Modifications (DML)

### INSERT
```sql
INSERT INTO <relation> VALUES (<list of values>);
INSERT INTO <relation>(attr1, attr2) VALUES (val1, val2);
INSERT INTO <relation> (<subquery>);
```

### DELETE
```sql
DELETE FROM <relation> WHERE <condition>;
```

### UPDATE
```sql
UPDATE <relation> SET <attribute assignments> WHERE <condition>;
```

## Practice Problems

1. Find employees in New York departments, including departments with no employees.
2. Find top 3 most expensive beers for each bar selling more than 5 beers.
3. Find customers with above-average order amounts in cities with >10 customers.
4. Insert high-value customers (>$10,000 spent) into a new table.
5. Increase beer prices by 10% in bars with below-average prices.

## Answer Sheet

1. Employee-Department Join:
```sql
SELECT e.id, e.name, d.dept_name
FROM Department d
LEFT OUTER JOIN Employee e ON d.dept_id = e.dept_id
WHERE d.location = 'New York';
```

2. Top 3 Expensive Beers:
```sql
WITH RankedBeers AS (
  SELECT bar, beer, price,
         RANK() OVER (PARTITION BY bar ORDER BY price DESC) as price_rank,
         COUNT(*) OVER (PARTITION BY bar) as beer_count
  FROM Sells
)
SELECT bar, beer, price
FROM RankedBeers
WHERE price_rank <= 3 AND beer_count > 5;
```

3. Above-Average Customers:
```sql
WITH CityAvg AS (
  SELECT c.city, AVG(o.total_amount) as avg_amount,
         COUNT(DISTINCT c.customer_id) as customer_count
  FROM Customers c
  JOIN Orders o ON c.customer_id = o.customer_id
  GROUP BY c.city
  HAVING COUNT(DISTINCT c.customer_id) > 10
)
SELECT c.customer_id, c.name, c.city, AVG(o.total_amount) as avg_order_amount
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
JOIN CityAvg ca ON c.city = ca.city
GROUP BY c.customer_id, c.name, c.city, ca.avg_amount
HAVING AVG(o.total_amount) > ca.avg_amount;
```

4. Insert High-Value Customers:
```sql
INSERT INTO HighValueCustomers (customer_id, name, total_spent)
SELECT c.customer_id, c.name, SUM(o.total_amount) as total_spent
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name
HAVING SUM(o.total_amount) > 10000;
```

5. Update Beer Prices:
```sql
WITH BarAvg AS (
  SELECT bar, AVG(price) as avg_price
  FROM Sells
  GROUP BY bar
),
OverallAvg AS (
  SELECT AVG(avg_price) as overall_avg
  FROM BarAvg
)
UPDATE Sells s
SET price = price * 1.1
WHERE EXISTS (
  SELECT 1
  FROM BarAvg ba, OverallAvg oa
  WHERE ba.bar = s.bar AND ba.avg_price < oa.overall_avg
);
```

