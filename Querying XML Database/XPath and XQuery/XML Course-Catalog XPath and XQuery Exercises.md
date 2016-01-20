XML Course-Catalog XPath and XQuery Exercises
====================================================================
Q1. Return all Title elements (of both departments and courses). 

```XQuery
doc("courses.xml")//Title
```


Q2. Return last names of all department chairs. 

```XQuery
doc("courses.xml")/Course_Catalog/Department/Chair/Professor/Last_Name
```


Q3. Return titles of courses with enrollment greater than 500. 

```XQuery
doc("courses.xml")/Course_Catalog/Department/Course[@Enrollment > 500]/Title
```


Q4. Return titles of departments that have some course that takes "CS106B" as a prerequisite. 

```XQuery
doc("courses.xml")/Course_Catalog/Department[Course/Prerequisites/Prereq="CS106B"]/Title
```


Q5. Return last names of all professors or lecturers who use a middle initial. Don't worry about eliminating duplicates. 

```XQuery
doc("courses.xml")/Course_Catalog/Department//(Professor|Lecturer)[Middle_Initial]/Last_Name
```


Q6. Return the count of courses that have a cross-listed course (i.e., that have "Cross-listed" in their description). 

```XQuery
doc("courses.xml")/Course_Catalog/count(Department/Course[contains(Description, "Cross-listed")])
```


Q7. Return the average enrollment of all courses in the CS department. 

```XQuery
doc("courses.xml")/Course_Catalog/Department[@Code="CS"]/avg(Course/@Enrollment)
```


Q8. Return last names of instructors teaching at least one course that has "system" in its description and enrollment greater than 100.

```XQuery
doc("courses.xml")/Course_Catalog/Department/Course[@Enrollment > 100 and contains(Description, "system")]/Instructors//Last_Name
```

Q9. Return the title of the course with the largest enrollment. 

```XQuery
doc("courses.xml")/Course_Catalog//Course[@Enrollment = max(doc("courses.xml")/Course_Catalog//Course/@Enrollment)]/Title
```
====================================================================