---
title: S3 Select query language syntax
description: This article describes the syntax of the S3 Select query language.
---

# S3 Select query language syntax

{% note info %}

To be able to make S3 Select requests, contact [support](../../support/overview.md).

{% endnote %}

The only standard SQL operator used by S3 Select is `SELECT`. It supports the following standard ANSI clauses:

* SELECT
* FROM
* WHERE
* LIMIT

S3 Select does not support nested queries and joins.

## SELECT {#select-list}

This operator determines the object data returned by a query, such as names of columns, functions, and expressions.

Syntax:

```sql
SELECT *
SELECT projection [ AS column_alias | column_alias ] [, ...]
```

The first form of the clause returns each row that satisfies the condition in the `WHERE` clause as is. The second one returns a row with a user-defined projection of the output scalar expressions for each column.


## FROM {#from-clause}

`FROM` clauses provide data for `SELECT`. As an argument, they take the name of an {{ objstorage-name }} object.

Syntax:

```sql
FROM S3Object
FROM S3Object alias
FROM S3Object AS alias
```

As in standard SQL, the `FROM` clause creates rows that are filtered in the `WHERE` clause and projected in the `SELECT` list.


## WHERE {#where-clause}

This clause filters rows based on a condition, which you specify as a logical expression. The returned result only contains rows for which the expression equals to `TRUE`.

Syntax:

```sql
WHERE condition
```


## LIMIT {#limit-clause}

This clause limits the number of records returned by a query.

Syntax:

```sql
LIMIT number
```


## Attribute access {#attribute-access}

`SELECT` and `WHERE` clauses can refer to record data by using file attributes.

CSV file attributes:

* Column numbers.

  In a query, you can refer to a specific column using the `_N` name, where `N` is the column position in a file.

  The position count starts at 1. For example, if the first column's name is `_1`, the second one's name is `_2`.

  You can refer to a column as `_N` or `alias._N`. For example, the `_2` and `myAlias._2` names are both valid references to a column in the `SELECT` and `WHERE` clauses.

* Column headers.

  As in conventional SQL, expressions in `SELECT` and `WHERE` clauses may refer to columns by the column header, i.e., `alias.column_name` or `column_name`.

JSON file attributes:

* Document.

  You can access JSON file fields by the field name, i.e., `alias.name`.


**Examples**

Sample data in JSON format:

```json
{"timestamp":"2021-02-26T01:27:19Z","object_key":"name1","status":404,"request_time":16}
{"timestamp":"2021-02-26T01:27:19Z","object_key":"name2","status":200,"request_time":12}
{"timestamp":"2021-02-26T01:27:20Z","object_key":"name3","status":200,"request_time":6}
```

Query:

```sql
SELECT "timestamp", object_key, request_time FROM S3Object WHERE status >= 400
```

Result:

```json
{"timestamp":"2021-02-26T01:27:19Z","object_key":"name1","request_time":16}
```

Query:

```sql
SELECT * FROM S3Object WHERE request_time >= 10
```

Result:

```json
{"timestamp":"2021-02-26T01:27:19Z","object_key":"name1","status":404,"request_time":16}
{"timestamp":"2021-02-26T01:27:19Z","object_key":"name2","status":200,"request_time":12}
```


## Case sensitivity of header and attribute names {#sensitivity}

To indicate that CSV file column headers or JSON file attributes are case-sensitive, use double quotes. Headers or object attributes without double quotes are not case-sensitive. Therefore, a query error may occur if a field name is ambiguous.

**Examples**

1. A queried object has the **NAME** header or attribute.

    If there is no indication of case sensitivity, the query successfully returns the object data:

    ```sql
    SELECT s.name FROM S3Object s
    ```

    If you enclose the header or attribute in double quotes, the query will result in a 400 error (`MissingHeaderName`):

    ```sql
    SELECT s."name" FROM S3Object s
    ```

1. A queried object has one header or attribute called **NAME** and another header or attribute called **name**.

    If there is no indication of case sensitivity, there is ambiguity as to which header or attribute to select. The query returns a 400 error (`AmbiguousFieldName`):

    ```sql
    SELECT s.name FROM S3Object s
    ```

    If you enclose the header or attribute in double quotes, the query will successfully return the object data:

    ```sql
    SELECT s."NAME" FROM S3Object s
    ```


## Reserved keywords {#reserved-keywords}

S3 Select has a set of reserved keywords that are required to run SQL expressions when querying object contents. Reserved keywords include function names, data type names, operators, etc.

In some cases, user-defined terms may clash with a reserved keyword. To avoid conflicts, use double quotes to indicate that you use a certain term intentionally. Otherwise, a 400 syntax error will occur.

**Examples**

A queried object has a header or attribute called **CAST**, which is a reserved keyword.

If you enclose a user-defined header or attribute in double quotes, the query will successfully return the object data:

```sql
SELECT s."CAST" FROM S3Object s
```

If you do not enclose a user-defined header or attribute in double quotes, there will be a conflict with the reserved keyword. In which case the query will return a 400 syntax error:

```sql
SELECT s.CAST FROM S3Object s
```

## Scalar expressions {#scalar-expressions}

`WHERE` and `SELECT` clauses may contain SQL scalar expressions returning scalar values. These may appear as follows:

* `literal`. SQL literal, which is an explicit numeric, character, string, or Boolean value (constant) not represented by an ID.

* `column_reference`. Reference to a column in `column_name` or `alias.column_name` format used to access the column using the column header.

  Example:

  ```sql
  SELECT city.name FROM S3Object city
  ```

* `unary_op expression`. In this expression, `unary_op` is a unary SQL operator. Unary operators perform operations on a single operand. They include, e.g., the unary minus that changes the sign of a number.

  Example:

  ```sql
  SELECT -5 FROM S3Object
  ```

* `expression binary_op expression`. In this expression, `binary_op` is a binary SQL operator. Binary operators perform an operation on two operands. Binary operators include, e.g., arithmetic, logical, and comparison operators.

  Examples:

  ```sql
  SELECT x FROM S3Object WHERE x=3
  ```

  ```sql
  SELECT result FROM S3Object WHERE result>=1 AND result<=5
  ```

* `func_name`. In this expression, `func_name` is the name of a callable scalar function.

  Example:

  ```sql
  SELECT CAST(status AS INT) FROM S3Object
  ```

* `expression [ NOT ] BETWEEN expression AND expression`. Checks if a value belongs to a range.

  Example:

  ```sql
  SELECT x FROM S3Object WHERE x BETWEEN -1 AND 1
  ```

## Aggregate functions {#aggregate-functions}

In `SELECT` clauses, you can use _aggregate functions_ that are calculated using values of multiple or all rows and return a single resulting value.

The following functions are supported:

| Function | Description | Input type | Output type |
| ----- | ----- | ----- | ----- |
| `COUNT` | Number of rows | Any | `INT` |
| `MIN` | Minimum value within a certain set of values | `INT` or `DECIMAL` | Same as input |
| `MAX` | Maximum value within a certain set of values | `INT` or `DECIMAL` | Same as input |
| `SUM` | Sum of values | `INT`, `FLOAT`, or `DECIMAL` | Same as input |
| `AVG` | Average value | `INT`, `FLOAT`, or `DECIMAL` | `DECIMAL` if the input type is `INT`;<br/>otherwise, same as input. |

Examples:

{% list tabs group=data_format %}

- JSON {#json}

  Sample data:

  ```json
  {"timestamp":"2021-02-26T01:27:19Z","object_key":"name1","status":404,"request_time":16}
  {"timestamp":"2021-02-26T01:27:19Z","object_key":"name2","status":200,"request_time":12}
  {"timestamp":"2021-02-26T01:27:20Z","object_key":"name3","status":200,"request_time":6}
  ```

  Query using all aggregate functions:

  ```sql
  SELECT
    COUNT(*) AS "count",
    MIN(request_time) AS "min",
    MAX(request_time) AS "max",
    SUM(request_time) AS "sum",
    AVG(request_time) AS "avg"
  FROM S3Object
  WHERE status = 200
  ```

  Result:

  ```json
  {"count": 2, "min": 6, "max": 12, "sum": 18, "avg": 9.0}
  ```

- CSV {#csv}

  Sample data:

  ```csv
  timestamp,object_key,status,request_time
  2021-02-26T01:27:19Z,name1,404,16
  2021-02-26T01:27:19Z,name2,200,12
  2021-02-26T01:27:20Z,name3,200,6
  ```

  Query using all aggregate functions:

  ```sql
  SELECT
    COUNT(*) AS "count",
    MIN(CAST(request_time AS FLOAT)) AS "min",
    MAX(CAST(request_time AS FLOAT)) AS "max",
    SUM(CAST(request_time AS FLOAT)) AS "sum",
    AVG(CAST(request_time AS FLOAT)) AS "avg"
  FROM S3Object
  WHERE status = '200'
  ```

  Since all values in the input CSV files are treated as strings, you need to convert them to the appropriate types using the `CAST` function.

  Result:

  ```text
  count,min,max,sum,avg
  2,6,12,18,9.0
  ```

{% endlist %}


## Use cases {#examples}

* [{#T}](../tutorials/server-logs.md)
* [{#T}](../tutorials/user-agent-statistics.md)
* [{#T}](../tutorials/billing-resource-detailing.md)

