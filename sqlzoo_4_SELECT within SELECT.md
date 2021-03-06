table world:

|       name|continent|    area|  population|            gdp|
|         -:|       -:|      -:|          -:|             -:|
|Afghanistan|	  Asia|	 652230|	25500100|  	 20343000000|
|    Albania|	Europe|	  28748|	 2831741|	 12960000000|
|    Algeria|	Africa|	2381741|	37100000|	188681000000|
|           |	      |	       |	        |               |

## Exercises

1. List each country name where the population is larger than that of 'Russia'.

        SELECT name FROM world
        WHERE population > (
                            SELECT population FROM world
                            WHERE name='Russia'
                            )
      
2. Show the countries in Europe with a per capita GDP greater than 'United Kingdom'.

        select name from world
        where continent like 'Europe' 
          and (
                gdp/population > (
                                    select gdp/population from world 
                                    where name like 'United Kingdom'
                                 )
              )

3. List the name and continent of countries in the continents containing either Argentina or Australia. Order by name of the country.

        select name, continent from world
        where continent in (
                            select continent from world 
                            where name in ('Argentina','Australia')
                            )
        order by name

4.  Which country has a population that is more than Canada but less than Poland? Show the name and the population.
        
        select name, population from world
        where population > (
                            select population from world 
                            where name like 'Canada'
                            )
          and population < (
                            select population from world 
                            where name like 'Poland'
                            )

5. Show the name and the population of each country in Europe. Show the population as a percentage of the population of Germany.

        select name,
               concat(round(population/(
                                        select population from world 
                                        where name like 'Germany')*100,0),'%'
                                        ) as percentage
        from world
        where continent like 'Europe' 

6. Which countries have a GDP greater than every country in Europe? [Give the name only.] (Some countries may have NULL gdp values)

        select name from world
        where gdp > all(
                        select gdp from world 
                        where gdp > 0 
                          and continent like 'Europe'
                        )
        # all 对所有数据都满足条件，才成立

7. Find the largest country (by area) in each continent, show the continent, the name and the area:

        #  讲一个表格看做两个：x、y，按相同的大陆（y.continent=x.continent）进行area的比较
        SELECT continent, name, area FROM world x
        WHERE area >= ALL(
                        SELECT area FROM world y
                        WHERE y.continent=x.continent 
                          AND area>0
                        )
  
8. List each continent and the name of the country that comes first alphabetically.

        SELECT continent,name FROM world x
        WHERE name = (
                    SELECT name FROM world y 
                    WHERE x.continent=y.continent 
                    ORDER BY name LIMIT 1
                    )
                  # limmit 返回数据的某些行，本例中返回第一行
         
9. Find the continents where all countries have a population <= 25000000.    Then find the names of the countries associated with these continents.    Show name, continent and population.

        select name,
               continent,
               population 
        from world as x
        where 25000000 >= all(
                                select population from world as y 
                                where x.continent = y.continent
                                  and population > 0
                             )

10. Some countries have populations more than three times that of any of their neighbours (in the same continent).Give the countries and continents.

        select name,
               continent 
        from world as x
        where x.population/3 >= all(
                                    select population from world as y 
                                    where x.continent = y.continent
                                      and x.name != y.name
                                      and y.population > 0
                                    )
