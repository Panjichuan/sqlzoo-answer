nobel:

|  yr|   subject|                     winner|
|---:|---------:|--------------------------:|
|1960| Chemistry|          Willard F. Libby |
|1960|Literature|           Saint-John Perse|
|1960|  Medicine|Sir Frank Macfarlane Burnet|
|    |          |                           |

## nobel laureates
We continue practicing simple SQL queries on a single table 

This tutorial is concerned with a table of Nobel prize winners:

**nobel**(yr,subject,winner)

Using the Select statement

## Winners from 1950 ##

1. Change the query shown so that it displays Nobel prizes for 1950.
        
        SELECT yr, subject, winner FROM nobel
        WHERE yr = 1950

2. Show who won the 1962 prize for Literature.

        SELECT winner FROM nobel
         WHERE yr = 1962
           AND subject = 'Literature'
   
3. Show the year and subject that won 'Albert Einstein' his prize.

        select yr, subject from nobel
        where winner = 'Albert Einstein'

4. Give the name of the 'Peace' winners since the year 2000, including 2000.
        
        select winner from nobel
        where subject = 'Peace'
          and yr >= 2000

5. Show all details (yr, subject, winner) of the Literature prize winners for 1980 to 1989 inclusive

        select yr, subject, winner
        from nobel
        where (subject = 'Literature')
          and (yr between 1980 and 1989)

6. Show all details of the presidential winners:Theodore Roosevelt\Woodrow Wilson\Jimmy Carter\Barack Obama

        SELECT * 
        FROM nobel
        WHERE winner 
                in ('Theodore Roosevelt',
                    'Woodrow Wilson',
                    'Jimmy Carter',
                    'Barack Obama')
                    
7. Show the winners with first name John

        select winner from nobel
        where left(winner,4) = 'John'

8. Show the Physics winners for 1980 together with the Chemistry winners for 1984.

        select * 
        from nobel
        where 
            (subject = 'Physics' and yr = 1980)
        or 
            (subject = 'Chemistry' and yr = 1984)

9. Show the winners for 1980 excluding the Chemistry and Medicine

        select * 
        from nobel
        where yr = 1980 
          and subject != 'Chemistry' 
          and subject != 'Medicine' 

10.  Show who won a 'Medicine' prize in an early year (before 1910, not including 1910) together with winners of a 'Literature' prize in a later year (after 2004, including 2004)

            select * from nobel
            where (subject = 'Medicine' and yr < 1910)
               or (subject ='Literature' and yr >=2004)

11. Find all details of the prize won by PETER GRÜNBERG

        select * from nobel
        where winner = 'PETER GRÜNBERG'

12. Find all details of the prize won by EUGENE O'NEILL

        SELECT * FROM nobel 
        WHERE winner='EUGENE O''NEILL' 
        # 如果字符串中存在单引号，在引用时应使用双引号表示

13. List the winners, year and subject where the winner starts with Sir. Show the the most recent first, then by name order.

        select winner, yr, subject from nobel
        where left(winner,3) = 'sir'
        order by yr DESC,winner ASC # DESC代表降序排列，ASC代表升序排列

14. Show the 1984 winners and subject ordered by subject and winner name; but list Chemistry and Physics last.

        SELECT winner, subject FROM nobel
        WHERE yr=1984
        ORDER BY subject IN ('Physics','Chemistry'), 
                 subject,winner
        # subject IN ('Physics','Chemistry')为一布尔型判断，
        # 如果subject是physics或chemistry，则返回1，不是，则返回0，相当于增加一个隐藏列，对其排序
        # order by 默认的排序方式为升序排列，使用DESC代表降序排列，ASC代表升序排列
