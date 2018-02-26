## Movie Database
This tutorial introduces the notion of a join. The database consists of three tables movie , actor and casting

|movie|	actor|	casting
|-----|------|---------
|id	  | id	 |  movieid
|title|	name |  actorid
|yr	  | ord  |
|director|   |		
|budget|     |		
|gross|      |		


1. List the films where the yr is 1962 [Show id, title]
  
        SELECT id, title
        FROM movie
        WHERE yr=1962

2. Give year of 'Citizen Kane'.
  
        select yr from movie
        where title like 'Citizen Kane'

3. List all of the Star Trek movies, include the id, title and yr . Order results by year.
  
        select id,title,yr from movie
        where title like ('%Star Trek%')
        order by yr desc # 降序排列，默认为升序

4. What id number does the actor 'Glenn Close' have?
  
        select id from actor
        where name like 'Glenn Close'

5. What is the id of the film 'Casablanca'
  
        select id from movie
        where title like 'Casablanca'
  
6. Obtain the cast list for 'Casablanca'.Use movieid=11768
  
        select actor.name 
        from actor join casting on id=actorid
        where movieid=11768

7. Obtain the cast list for the film 'Alien'
    
    方法1：连接casting和movie，通过电影名称‘Alien’获取actorid的view，再将这个view与actor通过actorid连接，获得相应的演员名单
        
        select actor.name 
        from actor join (
                        select casting.actorid from
                        movie join casting on movie.id=movieid
                        where title like 'Alien'
                        ) 
                        as actorid
                        on actor.id=actorid

    方法2：先获取‘Alien’的movieid，再在actor和casting连接的view中通过取得的movieid过滤出结果
        
        select actor.name
        from actor join casting on (actor.id = casting.actorid)
        where movieid=(select movie.id 
                       from movie
                       where movie.title = 'Alien')
  
8. List the films in which 'Harrison Ford' has appeared

    方法1：原理与7类同
        
        select movie.title as film
        from movie join (
                        select casting.movieid 
                        from casting 
                        join actor on casting.actorid=actor.id
                        where actor.name like 'Harrison Ford'
                        )
                        as movieid
                        on movie.id=movieid
  
    方法2：原理与7类同
    
        select movie.title
        from movie join casting on (movie.id = casting.movieid)
        where casting.actorid = ( 
                                select actor.id 
                                from actor 
                                where actor.name = 'Harrison Ford'
                                )
  
9. List the films where 'Harrison Ford' has appeared - but not in the starring role.
    
    方法1：现将actor和casting连接，过去出满足条件的movieid的view，然后用movie与其连接，得到过滤后的结果
  
        select movie.title as film
        from movie join (
                        select casting.movieid 
                        from casting 
                        join actor on casting.actorid=actor.id
                        where actor.name like 'Harrison Ford' 
                          and ord!=1
                        )
                        as movieid
                    on movie.id=movieid
    
    方法2：直接连接movie和casting两个table，使用where过滤结果
    
        select movie.title
        from movie 
        join casting on (movie.id = casting.movieid)
        where casting.actorid = (
                                select actor.id 
                                from actor 
                                where actor.name = 'Harrison Ford'
                                )  
          and casting.ord <> 1
  
10. List the films together with the leading star for all 1962 films.
    
    方法1：view的嵌套，由最内层解析，逐层获取需要的条件用于过滤结果。
    1 casting和actor连接，获取ord=1（leading star）对应的actorid和movieid
    2 actor 与1中的view连接，获取actor name和movieid
    3 movie与2的view通过movieid连接，得到结果
  
        select movie.title,y.name
        from movie join (
                        select actor.name,x.movieid
                        from actor join (
                                        select casting.actorid as actorid,
                                               movieid
                                        from casting 
                                        join actor on casting.actorid=actor.id
                                        where ord=1
                                        ) as x
                                        on actor.id=x.actorid
                                        ) as y
                    on movie.id=y.movieid
        where yr=1962
    
    方法2：三个table通过相应的key连接，通过where过滤结果

        select  movie.title, 
                actor.name
        from movie join casting on (movie.id = casting.movieid)
                   join actor   on (actor.id = casting.actorid)
        where movie.yr = 1962 and casting.ord = 1
  
11. Which were the busiest years for 'John Travolta', show the year and the number of movies he made each year for any year in which he made more than 2 movies.
    
    方法1：三个table连接，where过滤条件，聚合函数计算结果后降序排列，执行后发现只有一个大于2，则limit 1，符合要求
  
        select yr,count(title) as num
        from movie  join casting on movie.id=movieid 
                    join actor   on actorid=actor.id
        where name='John Travolta'
        group by yr
        order by num desc
        limit 1

    方法2：三个table连接，where过滤后，聚合函数计算出不同年份的电影数，再使用having过滤，取最大值，得到结果
    
        select movie.yr,
               count(movie.id)
        from movie join casting on (movie.id = casting.movieid) 
                   join actor   on (actor.id = casting.actorid) 
        where actor.name = 'John Travolta' 
        group by movie.yr
        having count(movie.id)>= all(
                                    select count(movie.id) 
                                    from movie 
                                    join casting 
                                    on (movie.id = casting.movieid) 
                                    join actor   
                                    on (actor.id = casting.actorid) 
                                    where actor.name = 'John Travolta' 
                                    group by movie.yr
                                    )
  
12. List the film title and the leading actor for all of the films 'Julie Andrews' played in.

    方法：三个table连接，ord=1确定出所有的leading actor的内容，将聚合后的view与三个table连接且actor.name = 'Julie Andrews'的view取交集得到结果
  
        select movie.title, 
               actor.name
        from movie 
        join casting on (movie.id = casting.movieid)
        join actor   on (actor.id = casting.actorid)
        where casting.ord = 1
        group by movie.id,movie.title,actor.name
        having 
        movie.id in (
                    select movie.id from movie
                    join casting on (movie.id = casting.movieid)
                    join actor   on (actor.id = casting.actorid)
                    where actor.name = 'Julie Andrews'
                    )
                     
13. Obtain a list, in alphabetical order, of actors who've had at least 30 starring roles.
    方法1：actor与casting连接后，where过滤出starring role，根据name聚合后得到数据，在外部select中使用where过滤出大于30的，并排序

        select x.name 
        from (
                select actor.name,
                count(actor.name) as num
                from actor join casting on actor.id=actorid
                where casting.ord=1
                group by actor.name
             ) as x
        where x.num>=30
        order by x.name
        
    方法2：三个table连接过滤出starring role，根据actor.id聚合，再使用having过滤
  
        select actor.name
        from actor join casting on (actor.id = casting.actorid) 
                   join movie   on (movie.id = casting.movieid) 
        where casting.ord = 1
        group by actor.name
        having count(movie.id) >= 30
  
14. List the films released in the year 1978 ordered by the number of actors in the cast, then by title.
  
    方法：movie与casting连接，where过滤年份后，使用聚合函数计算actorid的计数，排序
  
        select movie.title,
               count(actorid) as counts 
        from movie join casting on movie.id=movieid
        where yr=1978
        group by movieid,movie.title
        order by counts desc,movie.title

15. List all the people who have worked with 'Art Garfunkel'.
    
    方法：三个table连接，过滤出不包含'Art Garfunkel'的结果，通过movieid和三个table连接均包含'Art Garfunkel'的view取交集，即的结果
  
        select distinct name 
        from movie join casting on movie.id=movieid
                   join actor   on actorid=actor.id
        where actor.name!='Art Garfunkel'
          and movieid in (
                            select movieid
                            from movie join casting on movie.id=movieid
                                       join actor   on actorid=actor.id
                            where actor.name='Art Garfunkel'
                        )
        order by x.name
  
