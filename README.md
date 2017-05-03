
# Basketball Illustrations of SQL Techniques

We will take a look at joins, windowing, and time series processing. 

## Name Table
testdb=# select * from name;
 playerid |      name      
----------+----------------
        1 | Stephen Curry
        2 | Kevin Durant
        3 | Klay Thompson
        4 | Draymond Green
        5 | Andre Iguodala
(5 rows)
## Threes Table
testdb=# select * from threes;
 playerid | threes 
----------+--------
        1 |    324
        2 |    117
        3 |    268
        4 |     81
        6 |     25
(5 rows)
# 1) Joins

Joins allow users to combine data from two tables into one. 

## Inner Join

Return records that have matching values in both tables. 

We use an inner join when we want the data where we have both the player name and number of threes they have made for a particular player. 
testdb=# select * from name inner join threes on name.playerid = threes.playerid;
 playerid |      name      | playerid | threes 
----------+----------------+----------+--------
        1 | Stephen Curry  |        1 |    324
        2 | Kevin Durant   |        2 |    117
        3 | Klay Thompson  |        3 |    268
        4 | Draymond Green |        4 |     81
(4 rows)
## Left (Outer) Join
Return all records from left table and matched records from the right table. 

We use left join to include results from inner join and results that are in name table, but does not have a match with row in threes table. Hence, Andre Iguodala is in the table. 
testdb=# select * from name left join threes on name.playerid = threes.playerid; 
 playerid |      name      | playerid | threes 
----------+----------------+----------+--------
        1 | Stephen Curry  |        1 |    324
        2 | Kevin Durant   |        2 |    117
        3 | Klay Thompson  |        3 |    268
        4 | Draymond Green |        4 |     81
        5 | Andre Iguodala |          |       
(5 rows)
## Right (Outer) Join

Return all records from right table and matched records from left table. 
testdb=# select * from name right join threes on name.playerid = threes.playerid;
 playerid |      name      | playerid | threes 
----------+----------------+----------+--------
        1 | Stephen Curry  |        1 |    324
        2 | Kevin Durant   |        2 |    117
        3 | Klay Thompson  |        3 |    268
        4 | Draymond Green |        4 |     81
          |                |        6 |     25
(5 rows)
## Full (Outer) Join

Return all records when there is a match in either left or right table
testdb=# select * from name full outer join threes on name.playerid = threes.playerid;
 playerid |      name      | playerid | threes 
----------+----------------+----------+--------
        1 | Stephen Curry  |        1 |    324
        2 | Kevin Durant   |        2 |    117
        3 | Klay Thompson  |        3 |    268
        4 | Draymond Green |        4 |     81
        5 | Andre Iguodala |          |       
          |                |        6 |     25
(6 rows)
## Cross Join 

Return all records of every row of first table with every row of second table. 
testdb=# select * from name cross join threes;
 playerid |      name      | playerid | threes 
----------+----------------+----------+--------
        1 | Stephen Curry  |        1 |    324
        2 | Kevin Durant   |        1 |    324
        3 | Klay Thompson  |        1 |    324
        4 | Draymond Green |        1 |    324
        5 | Andre Iguodala |        1 |    324
        1 | Stephen Curry  |        2 |    117
        2 | Kevin Durant   |        2 |    117
        3 | Klay Thompson  |        2 |    117
        4 | Draymond Green |        2 |    117
        5 | Andre Iguodala |        2 |    117
        1 | Stephen Curry  |        3 |    268
        2 | Kevin Durant   |        3 |    268
        3 | Klay Thompson  |        3 |    268
        4 | Draymond Green |        3 |    268
        5 | Andre Iguodala |        3 |    268
        1 | Stephen Curry  |        4 |     81
        2 | Kevin Durant   |        4 |     81
        3 | Klay Thompson  |        4 |     81
        4 | Draymond Green |        4 |     81
        5 | Andre Iguodala |        4 |     81
        1 | Stephen Curry  |        6 |     25
        2 | Kevin Durant   |        6 |     25
        3 | Klay Thompson  |        6 |     25
        4 | Draymond Green |        6 |     25
        5 | Andre Iguodala |        6 |     25
(25 rows)
# 2) Windowing

Window functions operate on set of rows and return single value for each row of underlying query. 

## Salary Table 
testdb=# select * from salary;
       name        |   team    | salary 
-------------------+-----------+--------
 Draymond Green    | Warriors  |   16.4
 Kevin Durant      | Warriors  |  26.54
 Klay Thompson     | Warriors  |   15.5
 Stephen Curry     | Warriors  |  12.11
 Lebron James      | Cavaliers |  30.96
 Kyrie Irving      | Cavaliers |  17.64
 Kevin Love        | Cavaliers |  21.17
 Gordon Hayward    | Jazz      |  15.41
 Derrick Favors    | Jazz      |  11.05
 Joe Johnson       | Jazz      |  11.00
 DeMar DeRozan     | Raptors   |  26.54
 Jonas Valanciunas | Raptors   |  14.38
 DeMarre Carroll   | Raptors   |  14.20
(13 rows)
Say we would like to have the average of all salaries from players within our table, salary. We would also like the result of this query to be added as a column to each of the entries within the table. Windowing can help in this case because our set of rows is the entire table and the result of our query will be added as a column. 
testdb=# select *, avg(salary) over() as avgSalary from salary;
       name        |   team    | salary |      avgsalary      
-------------------+-----------+--------+---------------------
 Draymond Green    | Warriors  |   16.4 | 17.9153846153846154
 Kevin Durant      | Warriors  |  26.54 | 17.9153846153846154
 Klay Thompson     | Warriors  |   15.5 | 17.9153846153846154
 Stephen Curry     | Warriors  |  12.11 | 17.9153846153846154
 Lebron James      | Cavaliers |  30.96 | 17.9153846153846154
 Kyrie Irving      | Cavaliers |  17.64 | 17.9153846153846154
 Kevin Love        | Cavaliers |  21.17 | 17.9153846153846154
 Gordon Hayward    | Jazz      |  15.41 | 17.9153846153846154
 Derrick Favors    | Jazz      |  11.05 | 17.9153846153846154
 Joe Johnson       | Jazz      |  11.00 | 17.9153846153846154
 DeMar DeRozan     | Raptors   |  26.54 | 17.9153846153846154
 Jonas Valanciunas | Raptors   |  14.38 | 17.9153846153846154
 DeMarre Carroll   | Raptors   |  14.20 | 17.9153846153846154
(13 rows)
Say we want the average salaries of each team. We would also like the result of each of the average salaires of each team to be added as a column, teamAverage to each entry. 
testdb=# select *, avg(salary) over (partition by team) as teamAverage from salary;
       name        |   team    | salary |     teamaverage     
-------------------+-----------+--------+---------------------
 Kevin Love        | Cavaliers |  21.17 | 23.2566666666666667
 Lebron James      | Cavaliers |  30.96 | 23.2566666666666667
 Kyrie Irving      | Cavaliers |  17.64 | 23.2566666666666667
 Joe Johnson       | Jazz      |  11.00 | 12.4866666666666667
 Derrick Favors    | Jazz      |  11.05 | 12.4866666666666667
 Gordon Hayward    | Jazz      |  15.41 | 12.4866666666666667
 DeMarre Carroll   | Raptors   |  14.20 | 18.3733333333333333
 DeMar DeRozan     | Raptors   |  26.54 | 18.3733333333333333
 Jonas Valanciunas | Raptors   |  14.38 | 18.3733333333333333
 Kevin Durant      | Warriors  |  26.54 | 17.6375000000000000
 Draymond Green    | Warriors  |   16.4 | 17.6375000000000000
 Stephen Curry     | Warriors  |  12.11 | 17.6375000000000000
 Klay Thompson     | Warriors  |   15.5 | 17.6375000000000000
(13 rows)
Say we want the average salaries of each team to be added as a column teamAverage like above. Suppose we would also like to know how each player's salary ranks among its teammates. 
testdb=# select *, row_number()  over (partition by team order by salary desc), avg(salary) over (partition by team) as teamAverage from salary;
       name        |   team    | salary | row_number |     teamaverage     
-------------------+-----------+--------+------------+---------------------
 Lebron James      | Cavaliers |  30.96 |          1 | 23.2566666666666667
 Kevin Love        | Cavaliers |  21.17 |          2 | 23.2566666666666667
 Kyrie Irving      | Cavaliers |  17.64 |          3 | 23.2566666666666667
 Gordon Hayward    | Jazz      |  15.41 |          1 | 12.4866666666666667
 Derrick Favors    | Jazz      |  11.05 |          2 | 12.4866666666666667
 Joe Johnson       | Jazz      |  11.00 |          3 | 12.4866666666666667
 DeMar DeRozan     | Raptors   |  26.54 |          1 | 18.3733333333333333
 Jonas Valanciunas | Raptors   |  14.38 |          2 | 18.3733333333333333
 DeMarre Carroll   | Raptors   |  14.20 |          3 | 18.3733333333333333
 Kevin Durant      | Warriors  |  26.54 |          1 | 17.6375000000000000
 Draymond Green    | Warriors  |   16.4 |          2 | 17.6375000000000000
 Klay Thompson     | Warriors  |   15.5 |          3 | 17.6375000000000000
 Stephen Curry     | Warriors  |  12.11 |          4 | 17.6375000000000000
(13 rows)
Say we want to have the sum of each team's salary to be added as a column to each of the entries. 
testdb=# select *, sum(salary) over(partition by team) from salary order by sum;
       name        |   team    | salary |  sum  
-------------------+-----------+--------+-------
 Joe Johnson       | Jazz      |  11.00 | 37.46
 Derrick Favors    | Jazz      |  11.05 | 37.46
 Gordon Hayward    | Jazz      |  15.41 | 37.46
 DeMarre Carroll   | Raptors   |  14.20 | 55.12
 DeMar DeRozan     | Raptors   |  26.54 | 55.12
 Jonas Valanciunas | Raptors   |  14.38 | 55.12
 Kevin Love        | Cavaliers |  21.17 | 69.77
 Lebron James      | Cavaliers |  30.96 | 69.77
 Kyrie Irving      | Cavaliers |  17.64 | 69.77
 Kevin Durant      | Warriors  |  26.54 | 70.55
 Draymond Green    | Warriors  |   16.4 | 70.55
 Stephen Curry     | Warriors  |  12.11 | 70.55
 Klay Thompson     | Warriors  |   15.5 | 70.55
(13 rows)
Say we want a new column max to be the max salary of one player from each team to be added to each entry in the table salary. 
testdb=# select *, max(salary) over(partition by team) from salary order by max desc, salary desc;
       name        |   team    | salary |  max  
-------------------+-----------+--------+-------
 Lebron James      | Cavaliers |  30.96 | 30.96
 Kevin Love        | Cavaliers |  21.17 | 30.96
 Kyrie Irving      | Cavaliers |  17.64 | 30.96
 DeMar DeRozan     | Raptors   |  26.54 | 26.54
 Kevin Durant      | Warriors  |  26.54 | 26.54
 Draymond Green    | Warriors  |   16.4 | 26.54
 Klay Thompson     | Warriors  |   15.5 | 26.54
 Jonas Valanciunas | Raptors   |  14.38 | 26.54
 DeMarre Carroll   | Raptors   |  14.20 | 26.54
 Stephen Curry     | Warriors  |  12.11 | 26.54
 Gordon Hayward    | Jazz      |  15.41 | 15.41
 Derrick Favors    | Jazz      |  11.05 | 15.41
 Joe Johnson       | Jazz      |  11.00 | 15.41
(13 rows)
Suppose we want a new column, cumedist that will represent the cumulative distribution of salaries for each team. 
testdb=# select *, cume_dist() over(order by salary) as cumedist from salary;
       name        |   team    | salary |      cumedist      
-------------------+-----------+--------+--------------------
 Joe Johnson       | Jazz      |  11.00 | 0.0769230769230769
 Derrick Favors    | Jazz      |  11.05 |  0.153846153846154
 Stephen Curry     | Warriors  |  12.11 |  0.230769230769231
 DeMarre Carroll   | Raptors   |  14.20 |  0.307692307692308
 Jonas Valanciunas | Raptors   |  14.38 |  0.384615384615385
 Gordon Hayward    | Jazz      |  15.41 |  0.461538461538462
 Klay Thompson     | Warriors  |   15.5 |  0.538461538461538
 Draymond Green    | Warriors  |   16.4 |  0.615384615384615
 Kyrie Irving      | Cavaliers |  17.64 |  0.692307692307692
 Kevin Love        | Cavaliers |  21.17 |  0.769230769230769
 DeMar DeRozan     | Raptors   |  26.54 |  0.923076923076923
 Kevin Durant      | Warriors  |  26.54 |  0.923076923076923
 Lebron James      | Cavaliers |  30.96 |                  1
(13 rows)
# 3) Time Series Processing
SQL Query to return what pages users came from

The original table contains three columns: pid, event, and time. The pid is the user that is accessing the web page called event. Time is when the user accessed the page. The query will return a table with three columns: prev, event, and count.

pid | event | time
-----+-------+---------------------

1 | A | 2016-09-23 02:21:13

2 | B | 2016-09-23 03:24:13

2 | C | 2016-09-23 04:21:13

3 | A | 2016-09-23 05:21:13

3 | B | 2016-09-23 02:21:13

1 | B | 2016-09-23 04:21:13

Prev is the web page that the user was on before clicking to enter web page in event. Count is how many times a user made a transition from the web page in prev to the web page in event.

prev | event | count

------+-------+-------

B | A | 1

A | B | 1

B | C | 1

```python
create view t3 as (
select
    rank() over (order by pid, time),
    pid, event, time
from test5);

select e1.prev, e1.event, count(*) 
from 
    eventtransitions as e1,
    (select a1.rank as r1,
        a2.rank as r2,
        a1.event as a1_event,
        a2.event as a2_event,
        a1.pid as a1_pid,
        a2.pid as a2_pid
    from t3 as a1, t3 as a2) as temp
where r1 = r2-1
and e1.event = a2_event
and e1.prev = a1_event
and a1_pid = a2_pid
group by e1.prev, e1.event;
```
