XML Course-Catalog XPath and XQuery Exercises Extras
=============================================================
Q1. Return the course number of the course that is cross-listed as "LING180". 

```XQuery
doc("courses.xml")//Course[contains(Description, 'LING180')]/data(@Number)
```


Q2. Return course numbers of courses that have the same title as some other course. (Hint: You might want to use the "preceding" and "following" navigation axes for this query, which were not covered in the video or our demo script; they match any preceding or following node, not just siblings.) 

```XQuery
for $c1 in doc("courses.xml")//Course
for $c2 in doc("courses.xml")//Course
where $c1/Title = $c2/Title and $c1/data(@Number) < $c2/data(@Number)
return ($c1/data(@Number), $c2/data(@Number))
```

Q3. Return course numbers of courses taught by an instructor with first name "Daphne" or "Julie". 

```XQuery
for $c in doc("courses.xml")//Course
where $c/Instructors//First_Name = "Daphne" or $c/Instructors//First_Name = "Julie"
return $c/data(@Number)
```


Q4. Return the number (count) of courses that have no lecturers as instructors. 

```XQuery
count(for $c in doc("courses.xml")//Course
      where count($c/Instructors/Lecturer) = 0
      return $c)
```


Q5. Return titles of courses taught by the chair of a department. For this question, you may assume that all professors have distinct last names. 

```XQuery
for $c in doc("courses.xml")//Course
where $c/Instructors//Last_Name = $c/parent::Department/Chair/Professor/Last_Name
return $c/Title
```


Q6. Return titles of courses that have both a lecturer and a professor as instructors. Return each title only once. 

```XQuery
for $c in doc("courses.xml")//Course
where count($c/Instructors/Lecturer) > 0 and count($c/Instructors/Professor) > 0
return $c/Title
```


Q7. Return titles of courses taught by a professor with the last name "Ng" but not by a professor with the last name "Thrun". 

```XQuery
for $c1 in (
    for $c2 in doc("courses.xml")//Course
    where every $lastname in $c2/Instructors/Professor/Last_Name satisfies $lastname != 'Thrun'
    return $c2)
where $c1/Instructors/Professor/Last_Name = "Ng"
return $c1/Title
```


Q8. Return course numbers of courses that have a course taught by Eric Roberts as a prerequisite. 

```XQuery
for $c1 in doc("courses.xml")//Course
where $c1/Prerequisites/Prereq =(
    for $c2 in doc("courses.xml")//Course
    let $instructor := $c2/Instructors
    where $instructor/First_Name = "Eric" and $instructor/Last_Name = "Roberts"
    return $c2/data(@Number))
return $c1/data(@Number)
```


Q9. Create a summary of CS classes: List all CS department courses in order of enrollment. For each course include only its Enrollment (as an attribute) and its Title (as a subelement). 

```XQuery
<Summary>
{
for $c in doc("courses.xml")//Department[@Code = 'CS']/Course
    order by xs:int($c/@Enrollment)
    return <Course Enrollment = "{$c/data(@Enrollment)}"> { $c/Title } </Course>
}
</Summary>
```


Q10. Return a "Professors" element that contains as subelements a listing of all professors in all departments, sorted by last name with each professor appearing once. The "Professor" subelements should have the same structure as in the original data. For this question, you may assume that all professors have distinct last names. Watch out -- the presence/absence of middle initials may require some special handling. (This problem is quite challenging; congratulations if you get it right.) 

```XQuery
<Professors>
{
    for $ln in distinct-values(doc("courses.xml")//Professor/Last_Name)
        let $p := doc("courses.xml")//Professor[Last_Name = $ln]
        let $fn := distinct-values($p/First_Name)
    order by $ln
    return <Professor> 
               <First_Name> {$fn} </First_Name>
               {for $mi in $p/Middle_Initial
                   return $mi
               }
               <Last_Name> {$ln} </Last_Name>
           </Professor>
}
</Professors>
```


Q11. Expanding on the previous question, create an inverted course listing: Return an "Inverted_Course_Catalog" element that contains as subelements professors together with the courses they teach, sorted by last name. You may still assume that all professors have distinct last names. The "Professor" subelements should have the same structure as in the original data, with an additional single "Courses" subelement under Professor, containing a further "Course" subelement for each course number taught by that professor. Professors who do not teach any courses should have no Courses subelement at all. (This problem is very challenging; extra congratulations if you get it right.) 

```XQuery
<Inverted_Course_Catalog>
{
    for $ln in distinct-values(doc("courses.xml")//Professor/Last_Name)
        let $p := doc("courses.xml")//Professor[Last_Name = $ln]
        let $fn := distinct-values($p/First_Name)
        let $c := doc("courses.xml")//Course[Instructors/Professor/Last_Name = $ln]
    order by $ln
    return <Professor> 
               <First_Name> {$fn} </First_Name>
               {for $mi in $p/Middle_Initial
                   return $mi
               }
               <Last_Name> {$ln} </Last_Name>
               {
               if (count($c) > 0) then
               <Courses> 
                   {
                   for $course in $c
                   return <Course> { $course/data(@Number) } </Course>
              }
               </Courses>
               else ()
               }
           </Professor>
}
</Inverted_Course_Catalog>
```
=============================================================