table game:

|id	 |  mdate       |	                 stadium|team1|	team2
|----|--------------|---------------------------|-----|-------
|1001|	8 June 2012	|   National Stadium, Warsaw|POL  |	 GRE
|1002|	8 June 2012	|  Stadion Miejski (Wroclaw)|RUS  |  CZE
|1003|	12 June 2012|  Stadion Miejski (Wroclaw)|GRE  |	 CZE
|1004|	12 June 2012|  National Stadium, Warsaw |POL  |  RUS
|    |              |                           |     | 

table goal:

|matchid|teamid |	player	         |gtime
|-------|-------|--------------------|-----
|1001	|POL	|Robert Lewandowski	 |17
|1001	|GRE	|Dimitris Salpingidis|51
|1002	|RUS	|Alan Dzagoev	     |15
|1002	|RUS	|Roman Pavlyuchenko	 |82
|       |       |                    |

table eteam

|id	|teamname |	coach
|---|---------|------
|POL|	Poland|	Franciszek Smuda
|RUS|	Russia|	Dick Advocaat
|CZE|	Czech | Republic Michal Bilek
|GRE|	Greece|	Fernando Santos
|   |         |

## JOIN and UEFA EURO 2012 ###
This tutorial introduces JOIN which allows you to use data from two or more tables. The tables contain all matches and goals from UEFA EURO 2012 Football Championship in Poland and Ukraine.

1. Modify it to show the matchid and player name for all goals scored by Germany. To identify German players, check for: teamid = 'GER'
  
        SELECT matchid,player FROM goal 
        WHERE teamid like 'GER' # 等同于team1='GER'
  
2. Show id, stadium, team1, team2 for just game 1012
  
        SELECT id,stadium,team1,team2
        FROM game
        where id like '1012'

3. Modify it to show the player, teamid, stadium and mdate and for every German goal.
  
        SELECT player,teamid,stadium,mdate
        FROM game JOIN goal ON (id=matchid)
        where goal.teamid like 'GER'

4. Show the team1, team2 and player for every goal scored by a player called Mario player LIKE 'Mario%'
  
        select team1,team2,player
        from game join goal on (id=matchid) 
        where player like 'Mario%'
        # table_a join table_b on a.key=b.key  a表和b表根据两者的键值（a.key=b.key）进行连接
        # 一些join方法说明：
        # JOIN: 如果表中有至少一个匹配，则返回行
        # INNER JOIN：左表和右表均匹配，则返回相应行
        # LEFT JOIN: 即使右表中没有匹配，也从左表返回所有的行
        # RIGHT JOIN: 即使左表中没有匹配，也从右表返回所有的行
        # FULL JOIN: 只要其中一个表中存在匹配，就返回行
  
5. Show player, teamid, coach, gtime for all goals scored in the first 10 minutes gtime<=10
  
        SELECT player, teamid, coach, gtime
        FROM goal join eteam on teamid = id
        WHERE gtime<=10

6. List the the dates of the matches and the name of the team in which 'Fernando Santos' was the team1 coach.
  
        select mdate,teamname
        from game join eteam on team1=eteam.id
        where team1 like (
                            select eteam.id from eteam 
                            where coach like 'Fernando Santos'
                        )

7. List the player for every goal scored in a game where the stadium was 'National Stadium, Warsaw'
  
        select player 
        from game join goal on 
        id=matchid
        and stadium='National Stadium, Warsaw'
  
8. show the name of all players who scored a goal against Germany
  
        SELECT distinct player
        FROM game JOIN goal ON matchid = id 
        WHERE teamid!='GER' and (team1 ='GER' or team2='GER')
   
9. Show teamname and the total number of goals scored.
  
        SELECT teamname, count(teamname)
        FROM eteam JOIN goal ON id=teamid
        GROUP BY teamname

10. Show the stadium and the number of goals scored in each stadium.
  
        select stadium,count(stadium) 
        from game join goal on id=matchid
        group by stadium

11. For every match involving 'POL', show the matchid, date and the number of goals scored.
  
        SELECT matchid,mdate,count(teamid)
        FROM game JOIN goal ON matchid = id   
        WHERE (team1 = 'POL' OR team2 = 'POL')
        group by matchid,mdate
        # 涉及‘POL’的比赛，则team1或team2为‘POL’，matchid为比赛的ID，
        每场比赛不同，针对matchid聚合并对goal.teamid计数（count），
        # 可统计‘POL’参与的每场比赛的进球数
        # 在sqlzoo中，不针对mdate聚合显示‘'gisq.game.mdate' isn't in GROUP BY’，
        # 这是因为单单针对matchid聚合的view中不存在mdate，因此同时针对mdate聚合

12. For every match where 'GER' scored, show matchid, match date and the number of goals scored by 'GER'
  
        select matchid,mdate,count(teamid) from
        game join goal on id=matchid
        where teamid like 'GER'
        group by matchid,mdate

13. List every match with the goals scored by each team as shown.
This will use "CASE WHEN" which has not been explained in any previous exercises
  
        SELECT mdate,
               team1,     
               SUM(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score1,     
               team2,
               SUM(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score2     
        FROM game left JOIN goal ON matchid = id 
        group by mdate, matchid, team1,team2
        # 列出每场比赛每支球队的进球数，因此应该关联game和goal表格，由于可能存在一场比赛不进球的情况，因此应使用left join
        # 每场比赛每支球队，即按照mdate,matchid分组group by
        # 使用case搜索函数，goal.teamid分别遍历team1和team2，并通过聚合函数运算sum计算得到每场比赛的进球数
