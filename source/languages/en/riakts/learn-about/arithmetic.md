---
title: Riak TS Arithmetic
project: riakts
version: 1.1.0+
document: guide
toc: true
index: true
audience: intermediate
---

Riak TS supports arithmetic operations in the select list.

As of Riak TS 1.1, arithmetic operations and aggregate functions cannot be mixed
in a single value expression.

Arithmetic operations default to 64-bit integer math, unless mixed with a
`double`, at which point they become floating-point.

### Numeric Literals

* **Since:** 1.1.0

Integer, decimal floating point, and exponent notation floating point
numeric literals are accepted:

```sql
SELECT 555, 1.1, 1e1, 1.123e-2 from GeoCheckin
WHERE time > 1452252523182 AND time < 1452252543182 AND myfamily = 'family1' AND myseries = 'series1'
```

| 555\<SINT64\> | 1.1\<DOUBLE\> | 10.0\<DOUBLE\> | 0.01123\<DOUBLE\> |
|-------------|-------------|--------------|-----------------|
| 555         | 1.1         | 10.0         | 0.01123         |

### Addition and Subtraction

* **Since:** 1.1.0

```sql
SELECT temperature, temperature + 1, temperature - 1 FROM GeoCheckin
WHERE time > 1452252523182 AND time < 1452252543182 AND myfamily = 'family1' AND myseries = 'series1'
```

| temperature\<DOUBLE\> | (temperature\+1)\<DOUBLE\> | (temperature\-1)\<DOUBLE\> |
|---------------------|-------------------------|-------------------------|
| 27.1                | 28.1                    | 26.1                    |

### Multiplication and Division

* **Since:** 1.1.0

```sql
SELECT temperature, temperature * 2, temperature / 2 from GeoCheckin
WHERE time > 1452252523182 AND time < 1452252543182 AND myfamily = 'family1' AND myseries = 'series1'
```

| temperature\<DOUBLE\> | (temperature\*2)\<DOUBLE\> | (temperature/2)\<DOUBLE\> |
|---------------------|-------------------------|-------------------------|
| 27.1                | 54.2                    | 13.55                   |

### Negation

* **Since:** 1.1.0

```sql
SELECT temperature, -temperature from GeoCheckin
WHERE time > 1452252523182 AND time < 1452252543182 AND myfamily = 'family1' AND myseries = 'series1'
```

| temperature\<DOUBLE\> | -temperature\<DOUBLE\> |
|---------------------|----------------------|
| 27.1                | -27.1                |

### Order of Operations

* **Since:** 1.1.0

```sql
SELECT temperature + 2 * 3, (temperature + 2) * 3 from GeoCheckin
WHERE time > 1452252523182 AND time < 1452252543182 AND myfamily = 'family1' AND myseries = 'series1'
```

| (temperature+(2\*3))\<DOUBLE\> | ((temperature\+2)\*3)\<DOUBLE\> |
|-----------------------------|-----------------------------|
| 33.1                        | 87.30000000000001           |


### Floating Point Odds and Ends

* **Since:** 1.1.0

Operations on floating point numbers that would return `Infinity` or `NaN` are
not supported.

For example, neither of these queries return successfully:

```sql
SELECT 0.0 / 0.0 from GeoCheckin
WHERE time > 1452252523182 AND time < 1452252543182 AND myfamily = 'family1' AND myseries = 'series1'

SELECT 1.0 / 0.0 from GeoCheckin
WHERE time > 1452252523182 AND time < 1452252543182 AND myfamily = 'family1' AND myseries = 'series1'
```

### Operations with Multiple Field References

* **Since:** 1.1.0

Operations involving two or more references to fields/columns are not supported.

This query will return an error:

```sql
SELECT temperature + temperature FROM GeoCheckin
WHERE time > 1452252523182 AND time < 1452252543182 AND myfamily = 'family1' AND myseries = 'series1'
```