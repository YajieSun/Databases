SQL Social-Network Query Exercises Extras
====================================================================================================
Q1. For every situation where student A likes student B, but student B likes a different student C, return the names and grades of A, B, and C. 

```SQL
select H1.name, H1.grade, H2.name, H2.grade, H3.name, H3.grade 
from Likes L1, Likes L2, Highschooler H1, Highschooler H2, Highschooler H3
on L1.ID2 = L2.ID1 and L1.ID1 = H1.ID and L1.ID2 = H2.ID and L2.ID2 = H3.ID
where L1.ID1 <> L2.ID2;
```

Q2. Find those students for whom all of their friends are in different grades from themselves. Return the students' names and grades. 

```SQL
select name, grade
from Highschooler
where ID not in 
    (select ID1 
    from Friend, Highschooler H1, Highschooler H2
    on ID1 = H1.ID and ID2 = H2.ID
    where H1.grade = H2.grade);
```

Q3. What is the average number of friends per student? (Your result should be just one number.) 

```SQL
select avg(F.num) 
from (select count(ID2) as num from Friend group by ID1) F;
```

Q4. Find the number of students who are either friends with Cassandra or are friends of friends of Cassandra. Do not count Cassandra, even though technically she is a friend of a friend. 

- *First select all ID's of students who are frineds with Cassandra.*
- *Then for each ID, if s/he has n friends, then the number of his/her friends who is not Cassandra is n - 1.*
- *We need to add the friend him/herself. So for a friend of Cassandra, the sum of friends with Cassandra (1) and  friends of friends of Cassandra (n - 1) is 1 + n - 1 = n*

```SQL
select count(ID2) 
from Friend 
where ID1 in 
    (select ID2 from Friend where ID1 = 
        (select ID from Highschooler where name = 'Cassandra'));
```

Q5. Find the name and grade of the student(s) with the greatest number of friends. 

```SQL
select name, grade 
from (select ID1, count(ID2) as num from Friend group by ID1) F left join Highschooler
on ID1 = ID
where num = (select max(num) from (select count(ID2) as num from Friend group by ID1));
```
====================================================================================================