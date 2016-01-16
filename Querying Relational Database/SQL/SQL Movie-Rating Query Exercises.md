SQL Movie-Rating Query Exercises
====================================================================
Q1. Find the titles of all movies directed by Steven Spielberg. 
```SQL
select title from Movie where director = 'Steven Spielberg'
```


Q2. Find all years that have a movie that received a rating of 4 or 5, and sort them in increasing order. 

```SQL
select distinct year from Movie, Rating 
where Movie.mID = Rating.mID and stars > 3 
order by year
```


Q3. Find the titles of all movies that have no ratings. 

```SQL
select title from Movie 
where mID not in (select mID from Rating)
```


Q4. Some reviewers didn't provide a date with their rating. Find the names of all reviewers who have ratings with a NULL value for the date. 

```SQL
select name from Reviewer 
where rID in (select rID from Rating where ratingDate is null)
```


Q5. Write a query to return the ratings data in a more readable format: reviewer name, movie title, stars, and ratingDate. Also, sort the data, first by reviewer name, then by movie title, and lastly by number of stars. 

```SQL
select name, title, stars, ratingDate 
from Movie, Reviewer, Rating 
where Movie.mID = Rating.mID and Rating.rID = Reviewer.rID 
order by name, title, stars
```


Q6. For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, return the reviewer's name and the title of the movie. 

```SQL
select name, title from Reviewer, Movie,
    (select r1.rID, r1.mID from Rating r1, Rating r2
    where r1.rID = r2.rID and r1.mID = r2.mID and 
    r1.ratingDate < r2.ratingDate and r1.stars < r2.stars) results
where results.rID = Reviewer.rID and Movie.mID = results.mID
```


Q7. For each movie that has at least one rating, find the highest number of stars that movie received.Return the movie title and number of stars. Sort by movie title. 

```SQL
select title, max(stars) from Movie, Rating 
where Movie.mID = Rating.mID
group by Rating.mID
order by title
```

Q8. For each movie, return the title and the 'rating spread', that is, the difference between highest and lowest ratings given to that movie. Sort by rating spread from highest to lowest, then by movie 
title. 

```SQL
select title, (max(stars) - min(stars)) as spread from Movie, Rating
where Movie.mID = Rating.mID
group by Rating.mID
order by spread desc, title
```


Q9. Find the difference between the average rating of movies released before 1980 and the average rating of movies released after 1980. (Make sure to calculate the average rating for each movie, then the average of those averages for movies before 1980 and movies after. Don't just calculate the overall average rating before and after 1980.) 

```SQL
select pre.pre1980 - post.post1980
from
(select avg(before) as pre1980 
    from (select avg(stars) as before from Rating, Movie 
    where Movie.mID = Rating.mID and year < 1980
    group by title)) as pre, 
(select avg(after) as post1980 
    from (select avg(stars) as after from Rating, Movie 
    where Movie.mID = Rating.mID and year >= 1980
    group by title)) as post;
```
====================================================================