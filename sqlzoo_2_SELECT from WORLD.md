## Country Profile ##
In this tutorial you will use the SELECT command on the table world:

## Introdution ##
The world is a SQL table,including name,continent,area,population,gdp as its columns, in this tutorial I will use SELECT command on this table

1. Observe the result of running this SQL command to show the name, continent and population of all countries.
2. 
        SELECT name, continent, population FROM world

2. Show the name for the countries that have a population of at least 200 million. 200 million is 200000000, there are eight zeros.

        SELECT name FROM world
            WHERE population >= 200000000

3. Give the name and the per capita GDP for those countries with a population of at least 200 million.
HELP:How to calculate per capita GDP
per capita GDP is the GDP divided by the population GDP/population

    select name,GDP/population from world
    where population >= 200000000 

4. Show the name and population in millions for the countries of the continent 'South America'.Divide the population by 1000000 to get population in millions.

        select name,population/1000000 from world
        where continent = 'South America'

5. Show the name and population for France, Germany, Italy

        select name, population from world
        where name in ('France','Germany','Italy')
        # The IN operator allows an item to be tested against a list of values.

6. Show the countries which have a name that includes the word 'United'

        select name from world
        where name like '%United%'  # % 通配符 

7. Two ways to be big: A country is big if it has an area of more than 3 million sq km or it has a population of more than 250 million.Show the countries that are big by area or big by population. Show name, population and area.

        select name, population, area from world
        where area > 3000000 or population > 250000000

8. Exclusive OR (XOR). Show the countries that are big by area or big by population but not both. Show name, population and area.Australia has a big area but a small population, it should be included.Indonesia has a big population but a small area, it should be included.China has a big population and big area, it should be excluded.United Kingdom has a small population and a small area, it should be excluded.

        select name, population, area from world
        where (area > 3000000 and population < 250000000) 
            or (area < 3000000 and population > 250000000) 

9. Show the name and population in millions and the GDP in billions for the countries of the continent 'South America'. Use the ROUND function to show the values to two decimal places.For South America show population in millions and GDP in billions both to 2 decimal places.

        select  name,
                round(population/1000000,2), 
                round(GDP/1000000000,2)
        from world
        where continent = 'South America'
        # round 函数用于把数值字段舍入为指定的小数位数  为正数时，舍入小数点右侧的小数位为负数时，
        舍入小数点左侧的整数位

10. Show the name and per-capita GDP for those countries with a GDP of at least one trillion (1000000000000; that is 12 zeros). Round this value to the nearest 1000.Show per-capita GDP for the trillion dollar countries to the nearest $1000.

        select  name,
                round(GDP/population,-3) as GDP 
        from world
        where GDP >= 1000000000000

11. Greece has capital Athens.Each of the strings 'Greece', and 'Athens' has 6 characters.Show the name and capital where the name and the capital have the same number of characters.
        
        SELECT name, capital from world
        where length(name) = length(capital)
        # length 确定字符串的位数，一定程度上等同于MATLAB的length和Python的len

12. The capital of Sweden is Stockholm. Both words start with the letter 'S'.Show the name and the capital where the first letters of each match. Don't include countries where the name and the capital are the same word.

        SELECT name, capital FROM world
        where left(name,1) = left(capital,1) 
          and name != capital
        # left 字符串截取函数之一，自左侧开始截取相应长度的字符串
        # 其他字符串截取函数：
        locate、position、right、substring_index、
        substring、replace、length

13. Equatorial Guinea and Dominican Republic have all of the vowels (a e i o u) in the name. They don't count because they have more than one word in the name.Find the country that has all the vowels and no spaces in its name.

        SELECT name FROM world
        WHERE name LIKE '%a%' 
          and name LIKE '%e%'
          and name LIKE '%i%'
          and name LIKE '%o%'
          and name LIKE '%u%'
          and name NOT LIKE '% %' # not like 排除在外
