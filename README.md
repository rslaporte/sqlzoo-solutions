# SQLZOO Solutions
My solutions for [SQLZOO tutorial](https://sqlzoo.net/) 

## Sections:
1. [SELECT basics](#select-basics)
2. [SELECT from WORLD](#select-from-world)
3. [SELECT from NOBEL](#select-from-nobel)
4. [SELECT in SELECT](#select-in-select)
5. [SUM and COUNT](#sum-and-count)
<!-- 6. [JOIN](#join)
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

## SELECT from NOBEL

**1. Winners from 1950**
```sql
SELECT name, capital
  FROM world
  WHERE LEN(name)=LEN(capital)
```
**2. 1962 Literature**
```sql
SELECT winner
  FROM nobel
  WHERE yr = 1962
    AND subject = 'Literature'
```
**3. Albert Einstein**
```sql
SELECT yr, subject
  FROM nobel
 WHERE winner = 'Albert Einstein'
```
**4. Recent Peace Prizes**
```sql
SELECT winner
  FROM nobel
 WHERE yr >= 2000 AND subject = 'Peace'
```
**5. Literature in the 1980's**
```sql
SELECT yr, subject, winner
  FROM nobel
WHERE yr >= 1980 AND yr <= 1989 AND subject = 'Literature'
```
**6. Only Presidents**
```sql
SELECT * FROM nobel
  WHERE winner IN ('Theodore Roosevelt', 'Woodrow Wilson','Jimmy Carter', 'Barack Obama')
```
**7. John**
```sql
SELECT winner FROM nobel
  WHERE winner LIKE 'John%'
```
**8. Chemistry and Physics from different years**
```sql
SELECT * FROM nobel
  WHERE (subject = 'Physics' AND yr = 1980) OR (subject = 'Chemistry' AND yr = 1984)
```
**9. Exclude Chemists and Medics**
```sql
SELECT * FROM nobel
  WHERE yr = 1980 AND subject NOT IN ('Chemistry', 'Medicine')
```
**10. Early Medicine, Late Literature**
```sql
SELECT * FROM nobel
 WHERE (subject = 'Medicine' AND yr < 1910) OR (subject = 'Literature' AND yr >= 2004)
```
**11. Umlaut**
```sql
SELECT * FROM nobel
 WHERE winner = 'PETER GRÜNBERG'
```
**12. Apostrophe**
```sql
SELECT * FROM nobel
 WHERE winner = 'EUGENE O''NEILL'
```
**13. Knights of the realm**
```sql
SELECT winner, yr, subject
  FROM nobel
  WHERE winner LIKE 'Sir%'
ORDER BY yr DESC, winner
```
**14. Chemistry and Physics last***
```sql
SELECT winner, subject
  FROM nobel
 WHERE yr=1984
 ORDER BY 
   CASE WHEN subject IN ('Chemistry', 'Physics') THEN 1 ELSE 0 END,
subject,
winner
```

## SELECT in SELECT
**1. Bigger than Russia**
```sql
SELECT name FROM world
  WHERE population >
     (SELECT population FROM world
      WHERE name='Russia')
```
**2. Richer than UK**
```sql
SELECT name FROM world
 WHERE gdp/population > (SELECT gdp/population FROM world 
                         WHERE name='United Kingdom') 
 AND continent = 'Europe'
```
**3. Neighbours of Argentina and Australia**
```sql
SELECT name, continent FROM world
 WHERE continent IN (SELECT continent FROM world
                     WHERE name IN ('Argentina', 'Australia'))
ORDER BY name
```
**4. Between Canada and Poland**
```sql
SELECT name, population FROM world
  WHERE population > (SELECT population FROM world WHERE name = 'Canada') AND
        population < (SELECT population FROM world WHERE name = 'Poland')
```
**5. Percentages of Germany**
```sql
SELECT name, 
       CONCAT(CAST(ROUND(population*100/(SELECT population 
                                      FROM world
                                      WHERE name='Germany'), 0) AS INT)
             , '%') AS 'percentage'
FROM world
WHERE continent = 'Europe'
```
**6. Bigger than every country in Europe**
```sql
SELECT name FROM world
  WHERE gdp > ALL(SELECT gdp 
                 FROM world
                WHERE continent='Europe' AND gdp > 0)
```
**7. Largest in each continent**
```sql
SELECT continent, name, area FROM world x
  WHERE area >= ALL(SELECT area 
                    FROM world y
                    WHERE y.continent=x.continent AND area > 0)'
```
**8. First country of each continent (alphabetically)**
```sql
SELECT continent, name 
   FROM world x
     WHERE name <= ALL
       (SELECT name FROM world y
           WHERE y.continent = x.continent)
ORDER BY continent
```
**9. **
```sql
SELECT name, continent, population
FROM world
WHERE continent IN (SELECT continent FROM world
                 GROUP BY continent
                 HAVING MAX(population) <= 25000000)
```
**10. **
```sql
SELECT name, continent FROM world x
WHERE population >= ALL(SELECT population*3
                       FROM world y
                       WHERE y.continent = x.continent
                       AND y.name != x.name)
```

## SUM and COUNT
**1. Total world population **
```sql
SELECT SUM(population)
  FROM world
```
**2. List of continents**
```sql
SELECT DISTINCT continent
  FROM world
```
**3. GDP of Africa**
```sql
SELECT SUM(gdp)
  FROM world
  WHERE continent = 'Africa'
```
**4. Count the big countries**
```sql
SELECT COUNT(area)
  FROM world
  WHERE area >= 1000000
```
**5. Baltic states population**
```sql
SELECT SUM(population)
  FROM world
  WHERE name IN ('Estonia', 'Latvia', 'Lithuania')
```
**6. Counting the countries of each continent**
```sql
SELECT continent, COUNT(name)
FROM world
GROUP BY continent
```
**7. Counting big countries in each continent**
```sql
SELECT continent, COUNT(name)
  FROM world
  WHERE population >= 10*POWER(10,6)
GROUP BY continent
```
**8. Counting big continents**
```sql
SELECT continent
  FROM world
  GROUP BY continent
  HAVING SUM(population) >= 100000000
```
