SQL Social-Network Query Exercises
===============================================================================
Q1. Find the names of all students who are friends with someone named Gabriel. 

```SQL
select name 
from Friend join Highschooler on ID = ID1 
where ID2 in 
    (select ID from Highschooler where name = 'Gabriel');
```

Q2. For every student who likes someone 2 or more grades younger than themselves, return that student's name and grade, and the name and grade of the student they like. 

```SQL
select n1, g1, n2, g2 from 
  (select h1.name as n1, h1.grade as g1, h2.name as n2, h2.grade as g2, h1.grade - h2.grade as diff 
  from Highschooler h1, Highschooler h2, Likes
  where ID1 = h1.ID and ID2 = h2.ID)
where diff >= 2;
```

Q3. For every pair of students who both like each other, return the name and grade of both students. Include each pair only once, with the two names in alphabetical order. 

```SQL
select H1.name, H1.grade, H2.name, H2.grade
from Likes L1, Likes L2, Highschooler H1, Highschooler H2
where L1.ID1 = H1.ID and L1.ID2 = H2.ID and 
	L1.ID1 = L2.ID2 and L1.ID2 = L2.ID1 and H1.name < H2.name;
```

Q4. Find all students who do not appear in the Likes table (as a student who likes or is liked) and return their names and grades. Sort by grade, then by name within each grade. 

```SQL
select name, grade from Highschooler
where ID not in (select ID1 from Likes
                 union
                 select ID2 from likes)
order by grade, name;
```

Q5. For every situation where student A likes student B, but we have no information about whom B likes (that is, B does not appear as an ID1 in the Likes table), return A and B's names and grades. 

```SQL
select H1.name, H1.grade, H2.name, H2.grade 
from Likes, Highschooler H1, Highschooler H2
on Likes.ID1 = H1.ID and Likes.ID2 = H2.Id
where ID2 not in 
    (select ID1 from Likes);
```

Q6. Find names and grades of students who only have friends in the same grade. Return the result sorted by grade, then by name within each grade. 

```SQL
select name, grade
from Highschooler
where ID not in (select distinct ID1 
                from Friend, Highschooler H1, Highschooler H2
                on Friend.ID1 = H1.ID and Friend.ID2 = H2.ID
                where H1.grade <> H2.grade)
order by grade, name;
```

Q7. For each student A who likes a student B where the two are not friends, find if they have a friend C in common (who can introduce them!). For all such trios, return the name and grade of A, B, and C. 

```SQL
select H1.name, H1.grade, H2.name, H2.grade, H3.name, H3.grade
from (select ID1,ID2 from Likes where ID2 not in 
        (select ID2 from Friend where Friend.ID1 = Likes.ID1)) U, 
        Friend F1, Friend F2, Highschooler H1, Highschooler H2, Highschooler H3
on U.ID1 = F1.ID1 and U.ID2 = F2.ID1 and U.ID1 = H1.ID and U.ID2 = H2.ID and F1.ID2 = H3.ID
where F1.ID2 = F2.ID2;
```


Q8. Find the difference between the number of students in the school and the number of different first names. 

```SQL
select count(distinct ID) - count(distinct name) from Highschooler;
```

Q9. Find the name and grade of all students who are liked by more than one other student. 

```SQL
select name, grade 
from Likes, Highschooler
on ID = ID2
group by ID2 
having count(ID1) > 1;
```
===============================================================================