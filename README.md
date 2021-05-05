# SQLZOO Solutions
My solutions for [SQLZOO tutorial](https://sqlzoo.net/) 

## Sections:
1. [SELECT basics](#select-basics)
2. [SELECT from WORLD](#select-from-world)
<!--3. [SELECT from NOBEL](#select-from-nobel)
4. [SELECT in SELECT](#select-in-select)
5. [SUM and COUNT](#sum-and-count)
6. [JOIN](#join)
7. [More JOIN](#more-join)
8. [Using NULL](#using-null)
9. [Self JOIN](#self-join)-->

## SELECT basics

**1. Introducing the world table of countries**
```sql
SELECT population FROM world
  WHERE name = 'Germany'
```
**2. Scandinavia**
```sql
SELECT name, population FROM world
  WHERE name IN ('Sweden', 'Norway', 'Denmark');
```
**3. Just the right size**
```sql
SELECT name, area FROM world
  WHERE area BETWEEN 200000 AND 250000
```

## SELECT from WORLD
**1. Introduction**
```sql
SELECT name, continent, population FROM world
```
**2. Large Countries**
```sql
SELECT name
  FROM world
  WHERE population >= 200*POWER(10,6)
```
**3. Per capita GDP**
```sql
SELECT name, gdp/population AS 'per capita GDP'
  FROM world
  WHERE population >= 200*POWER(10,6)
```
**4. South America In millions**
```sql
SELECT name, population/(POWER(10,6)) AS 'population'
  FROM world
  WHERE continent = 'South America'
```
**5. France, Germany, Italy**
```sql
SELECT name, population
  FROM world
  WHERE name IN ('France', 'Germany', 'Italy')
```
**6. United**
```sql
SELECT name FROM world
  WHERE name LIKE '%United%'
```
**7. Two ways to be big**
```sql
SELECT name, population, area
  FROM world
  WHERE area >= 3*POWER(10,6) OR population >= 250*POWER(10,6)
```
**8. One or the other (but not both)**
```sql
SELECT name, population, area
  FROM world
  WHERE (area >= 3*POWER(10,6) AND population < 250*POWER(10,6)) OR (area < 3*POWER(10,6) AND population >= 250*POWER(10,6))
```
**9. Rounding**
```sql
SELECT name, ROUND(population/POWER(10,6), 2) AS 'population', ROUND(gdp/POWER(10,9), 2) AS 'gdp'
  FROM world
  WHERE continent='South America'
```
**10. Trillion dollar economies**
```sql
SELECT name, ROUND(gdp/population, -3) AS 'per-capita GDP'
  FROM world
  WHERE gdp > 1000000000000
```
**11. Name and capital have the same length**
```sql
SELECT name, capital
  FROM world
  WHERE LEN(name)=LEN(capital)
```
**12. Matching name and capital**
```sql
SELECT name, capital
  FROM world
  WHERE LEFT(name, 1) = LEFT(capital, 1) AND name <> capital
```
**13. All the vowels**
```sql
SELECT name
   FROM world
   WHERE name LIKE '%a%' 
    AND name LIKE '%e%' 
    AND name LIKE '%i%' 
    AND name LIKE '%o%' 
    AND name LIKE '%u%' 
    AND name NOT LIKE '% %'
```
