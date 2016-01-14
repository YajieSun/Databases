SQL Movie-Rating Query Exercises Extras
=======================================================================
Q1. Find the names of all reviewers who rated Gone with the Wind. 

```SQL
select distinct name from Reviewer where rID in 
    (select rID from Rating where mID in 
        (select mID from Movie where title = "Gone with the Wind"))
```


Q2. For any rating where the reviewer is the same as the director of the movie, return the reviewer name, movie title, and number of stars.

```SQL
select name, title, stars 
from Rating join Reviewer using(rID) join Movie using(mID) 
where director = name
```


Q3. Return all reviewer names and movie names together in a single list, alphabetized. (Sorting by the first name of the reviewer and first word in the title is fine; no need for special processing on last names or removing "The".) 

```SQL
select name from 
    (select name from Reviewer 
    union 
    select title from Movie)
order by name
```


Q4. Find the titles of all movies not reviewed by Chris Jackson. 

```SQL
select title 
from Movie 
where mID not in 
    (select mID 
    from Reviewer join Rating using(rId) 
    where name = 'Chris Jackson')
```


Q5. For all pairs of reviewers such that both reviewers gave a rating to the same movie, return the names of both reviewers. Eliminate duplicates, don't pair reviewers with themselves, and include each pair only once. For each pair, return the names in the pair in alphabetical order. 

```SQL
select distinct r1.name, r2.name from
    (select * from Rating join Reviewer using(rID)) r1,
    (select * from Rating join Reviewer using(rID)) r2
where r1.mID = r2.mID and r1.rID <> r2.rID and r1.name < r2.name
```


Q6. For each rating that is the lowest (fewest stars) currently in the database, return the reviewer name, movie title, and number of stars. 

```SQL
select name, title, r1.stars 
from Rating r1, Reviewer, Movie 
where r1.rID = Reviewer.rID and Movie.mID = r1.mID and not exists 
    (select r2.stars from Rating r2 where r2.stars < r1.stars)
```


Q7. List movie titles and average ratings, from highest-rated to lowest-rated. If two or more movies have the same average rating, list them in alphabetical order. 

```SQL
select title, avg(stars) as avgRating 
from Rating join Movie using(mID) 
group by mID
order by avgRating desc, title
```


Q8. Find the names of all reviewers who have contributed three or more ratings. (As an extra challenge, try writing the query without HAVING or without COUNT.) 

```SQL
select name 
from Rating join Reviewer using(rID) 
group by rID 
having count(mID) >= 3
```

No HAVING or COUNT
```SQL
select distinct name
from Rating r1, Rating r2, Rating r3, Reviewer
on r1.rID = r2.rID and r2.rID = r3.rID and r1.rID = Reviewer.rID
where (r1.mID <> r2.mID or r1.ratingDate <> r2.ratingDate)
     and (r2.mID <> r3.mID or r2.ratingDate <> r3.ratingDate)
     and (r3.mID <> r1.mID or r3.ratingDate <> r1.ratingDate)
```


Q9. Some directors directed more than one movie. For all such directors, return the titles of all movies directed by them, along with the director name. Sort by director name, then movie title. (As an extra challenge, try writing the query both with and without COUNT.) 

```SQL
select title, director 
from Movie 
where director in 
    (select director 
    from Movie 
    group by director 
    having count(*) > 1)
order by director, title
```

Without COUNT
```SQL
select m1.title, m1.director
from Movie m1, Movie m2
on m1.director = m2.director
where m1.title <> m2.title
order by m1.director, m1.title
```


Q10. Find the movie(s) with the highest average rating. Return the movie title(s) and average rating. (Hint: This query is more difficult to write in SQLite than other systems; you might think of it as finding the highest average rating and then choosing the movie(s) with that average rating.) 
```SQL
select title, avg(stars) as avg 
from Movie join Rating using(mID) 
group by mID 
having avg = 
    (select max(R.avg) from (select avg(stars) as avg from Rating group by mID) R)
```


Q11. Find the movie(s) with the lowest average rating. Return the movie title(s) and average rating. (Hint: This query may be more difficult to write in SQLite than other systems; you might think of it as finding the lowest average rating and then choosing the movie(s) with that average rating.) 

```SQL
select title, avg(stars) as avg 
from Movie join Rating using(mID) 
group by mID 
having avg = 
    (select min(R.avg) from (select avg(stars) as avg from Rating group by mID) R)
```


Q12. For each director, return the director's name together with the title(s) of the movie(s) they directed that received the highest rating among all of their movies, and the value of that rating. Ignore movies whose director is NULL. 

```SQL
select director, title, max(stars) 
from Rating join Movie using(mID) 
where director is not null 
group by director
```
=======================================================================