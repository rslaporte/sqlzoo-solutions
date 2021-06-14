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
## JOIN
**1. **
```sql
SELECT matchid, player 
  FROM goal 
  WHERE teamid = 'GER'
```
**2. **
```sql
SELECT id,stadium,team1,team2
  FROM game
  WHERE id=1012
```
**3. **
```sql
SELECT player, teamid, stadium, mdate
  FROM game 
  JOIN goal ON (id=matchid)
  WHERE teamid = 'GER'
```
**4. **
```sql
SELECT team1, team2, player
  FROM game 
  JOIN goal ON (game.id=goal.matchid)
  WHERE player LIKE 'Mario%'
```
**5. **
```sql
SELECT player, teamid, coach, gtime
  FROM goal 
  JOIN eteam ON (goal.teamid=eteam.id)
  WHERE gtime<=10
```
**6. **
```sql
SELECT mdate, teamname
 FROM game 
 JOIN eteam ON (game.team1 = eteam.id)
 WHERE coach = 'Fernando Santos'
```
**7. **
```sql
SELECT player
 FROM game 
 JOIN goal ON (game.id = goal.matchid)
 WHERE stadium = 'National Stadium, Warsaw'
```
**8. **
```sql
SELECT DISTINCT player
 FROM game 
 JOIN goal ON game.id = goal.matchid
 WHERE (team1='GER' OR team2 = 'GER') AND teamid != 'GER'
```
**9. **
```sql
SELECT teamname, COUNT(player)
  FROM eteam 
  JOIN goal ON id=teamid
  GROUP BY teamname
```
**10. **
```sql
SELECT stadium, COUNT(stadium)
  FROM game JOIN goal ON game.id = goal.matchid
  GROUP BY stadium
```
**11. **
```sql
SELECT matchid, mdate, COUNT(matchid)
  FROM game 
  JOIN goal ON id = matchid 
  WHERE (team1 = 'POL' OR team2 = 'POL')
  GROUP BY matchid, mdate
```
**12. **
```sql
SELECT matchid, mdate, COUNT(matchid)
 FROM game 
 JOIN goal ON id = matchid
 WHERE teamid = 'GER'
 GROUP BY matchid, mdate
```
**13. **
```sql
SELECT DISTINCT mdate, team1,
  SUM(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score1,
  team2,
  SUM(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score2
  FROM game 
  LEFT JOIN goal ON game.id = goal.matchid
  GROUP BY mdate, matchid, team1, team2
```

## More JOIN
**1. 1962 movies**
```sql
SELECT id, title
  FROM movie
  WHERE yr=1962'
```
**2. When was Citizen Kane released?**
```sql
SELECT yr
  FROM movie
  WHERE title='Citizen Kane'
```
**3. Star Trek movies**
```sql
SELECT id, title, yr
  FROM movie
  WHERE title LIKE '%Star Trek%'
```
**4. id for actor Glenn Close**
```sql
SELECT id 
  FROM actor
  WHERE name='Glenn Close'
```
**5. id for Casablanca**
```sql
SELECT id 
  FROM movie
  WHERE title='Casablanca'
```
**6. Cast list for Casablanca**
```sql
SELECT name 
  FROM actor JOIN casting ON id = actorid
  WHERE movieid = 27
```
**7. Alien cast list**
```sql
SELECT name 
  FROM actor
  JOIN casting ON actor.id = casting.actorid
  JOIN movie ON movie.id = casting.movieid
  WHERE title = 'Alien'
```
**8. Harrison Ford movies**
```sql
SELECT title 
  FROM movie
  JOIN casting ON movie.id = casting.movieid
  JOIN actor ON casting.actorid = actor.id
  WHERE name = 'Harrison Ford'
```
**9. Harrison Ford as a supporting actor**
```sql
SELECT title 
  FROM movie
  JOIN casting ON movie.id = casting.movieid
  JOIN actor ON casting.actorid = actor.id
  WHERE name = 'Harrison Ford' AND ord != 1
```
**10. Lead actors in 1962 movies**
```sql
SELECT title, name 
  FROM movie
  JOIN casting ON movie.id = casting.movieid
  JOIN actor ON casting.actorid = actor.id
  WHERE yr = 1962 AND ord = 1
```
**11. Busy years for Rock Hudson**
```sql
SELECT yr,COUNT(title) 
  FROM movie 
  JOIN casting ON movie.id = movieid
  JOIN actor   ON actorid = actor.id
  WHERE name='Rock Hudson'
  GROUP BY yr
  HAVING COUNT(title) > 2
```
**12. Lead actor in Julie Andrews movies**
```sql
SELECT title FROM movie
  JOIN casting ON movie.id = casting.movieid
  JOIN actor ON casting.actorid = actor.id
  WHERE name = 'Harrison Ford' AND ord != 1
```
