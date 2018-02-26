table world:

|       name|continent|    area|  population|            gdp|
|         -:|       -:|      -:|          -:|             -:|
|Afghanistan|	  Asia|	 652230|	25500100|  	 20343000000|
|    Albania|	Europe|	  28748|	 2831741|	 12960000000|
|    Algeria|	Africa|	2381741|	37100000|	188681000000|
|           |	      |	       |	        |               |

Aggregate Functions:sum,count,avg

distinct:返回唯一值

order by:排序

### Practicing ###

1. Show the total population of the world.

        SELECT SUM(population) FROM world
 

2. List all the continents - just once each.
  
        select distinct continent from world # distinct 用于返回唯一不同的值

3. Give the total GDP of Africa
  
        select sum(gdp) from world
        where continent like 'Africa'

4. How many countries have an area of at least 1000000
  
        select count(name) from world
        where area >= 1000000
    
5. What is the total population of ('Estonia', 'Latvia', 'Lithuania')
  
        select sum(population) from world
        where name in ('Estonia', 'Latvia', 'Lithuania')
        # a list after 'in'
    
## Using GROUP BY and HAVING ##

6. For each continent show the continent and number of countries.
        
        select continent, count(name) from world
        group by continent

7. For each continent show the continent and number of countries with populations of at least 10 million.
        
        select continent,count(name) from world
        where population >= 10000000
        group by continent
        # where 条件查询子句在group by聚合之前作用于table world，
        且where 子句不支持aggregate functions

8. List the continents that have a total population of at least 100 million.
  
        select continent from world
        group by continent
        having sum(population) >= 100000000
        # having 查询子句对group by之后的table起作用，且支持aggregate functions
