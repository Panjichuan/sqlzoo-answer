## Edinburgh Buses
Details of this datebase

    stops(id,name)
    route(num,company,pos,stop)
    
*stops*|*route*|
-:|-:|
 id|num|
 name|company|
|| pos|
||stop|

### Practice

1.How many stops are in the database.

    select count(distinct id) from stops 

2.Find the id value for the stop 'Craiglockhart'

    select id from stops
      where name = 'Craiglockhart'

3.Give the id and the name for the stops on the '4' 'LRT' service.

    select stops.id,stops.name 
    from stops join route on stops.id=route.stop
    where num=4 and company='LRT'

### Routes and stops

4.The query shown gives the number of routes that visit either London Road (149) or Craiglockhart (53). 
  Run the query and notice the two services that link these stops have a count of 2. 
  Add a HAVING clause to restrict the output to these two routes.
  
    SELECT company, num, COUNT(*) # 返回表中的记录数
    FROM route WHERE stop=149 OR stop=53
    GROUP BY company, num
    having count(*) = 2
      
5.Execute the self join shown and observe that b.stop gives all the places you can get to from Craiglockhart, without changing routes. 
Change the query so that it shows the services from Craiglockhart to London Road.

    SELECT a.company, a.num, a.stop, b.stop
    FROM route a JOIN route b ON a.company=b.company AND a.num=b.num
    WHERE a.stop=53 and b.stop=149
    # 将一个表格看成两个视图（view）a和b，使用company和num连接两个view
 
6.Change the query so that the services between 'Craiglockhart' and 'London Road' are shown

    SELECT a.company, a.num, stopa.name, stopb.name
    FROM route a JOIN route b ON a.company=b.company AND a.num=b.num
         JOIN stops stopa ON (a.stop=stopa.id)
         JOIN stops stopb ON (b.stop=stopb.id)
    WHERE stopa.name='Craiglockhart' and stopb.name='London Road'
    # 再连接两个stops的视图，可以按照stop判断
 
7.Give a list of all the services which connect stops 115 and 137 ('Haymarket' and 'Leith')

    select distinct a.company,a.num # 有些具有往返路线，因此使用distinct
    from route a join route b on a.company=b.company and a.num=b.num
    where a.stop=115 and b.stop=137
    
8.Give a list of the services which connect the stops 'Craiglockhart' and 'Tollcross'

    select a.company,a.num 
    from route a join route b on a.company=b.company and a.num=b.num
         join stops stopa on a.stop=stopa.id
         join stops stopb on b.stop=stopb.id
    where stopa.name = 'Craiglockhart' and stopb.name = 'Tollcross'

9.Give a distinct list of the stops which may be reached from 'Craiglockhart' by taking one bus, 
  including 'Craiglockhart' itself, offered by the LRT company. 
  Include the company and bus no. of the relevant services.
  
    select stopb.name,a.company,a.num
    from route a join route b on a.company=b.company and a.num=b.num
         join stops stopa on a.stop=stopa.id
         join stops stopb on b.stop=stopb.id
    where stopa.name='Craiglockhart' and a.company='LRT'
      
10.Find the routes involving two buses that can go from Craiglockhart to Sighthill.
   Show the bus no. and company for the first bus, the name of the stop for the transfer,
   and the bus no. and company for the second bus.

    SELECT DISTINCT bus1.num, # distinct 根据num，company，name来去重
           bus1.company,
           name,
           bus2.num,
           bus2.company
    FROM   
        (
        SELECT
            start1.num,
            start1.company,
            stop1.stop 
        FROM route AS start1 
            JOIN route AS stop1   
            ON  start1.num = stop1.num   
                AND start1.company = stop1.company 
                AND start1.stop != stop1.stop # 排除停靠站为终点站的情况
            WHERE start1.stop = ( 
                                SELECT id FROM stops 
                                WHERE name = 'Craiglockhart'
                                )
        ) 
        AS bus1 
        # 按照车辆编号num和公司company内部关联route，得到在Craiglockhart停靠的所有车辆信息
        JOIN 
        (
        SELECT start2.num, 
               start2.company, 
               start2.stop 
        FROM route AS start2 
            JOIN route AS stop2
            ON  start2.num = stop2.num
                AND start2.company = stop2.company 
                AND start2.stop != stop2.stop # 排除停靠站为终点站的情况
            WHERE stop2.stop = (
                                SELECT id FROM stops
                                WHERE name = 'Sighthill'
                               )
        ) 
        AS bus2
        # 与buse1类似，where过滤出的是终点站为Sighthill的所有车辆信息
        ON bus1.stop = bus2.stop 
        # buse1的终点站为Sighthill，buse2的起始站为Sighthill，所以以两者的停靠站stop连接两个view
    JOIN stops ON bus1.stop = stops.id # buse1的停靠站
