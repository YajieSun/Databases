Relational Algebra Exercises
================================================================================
Q1. Find all pizzas eaten by at least one female over the age of 20. 

```SQL
\project_{pizza} (
    \select_{gender='female' and age > 20} Person \join Eats
);
```


Q2. Find the names of all females who eat at least one pizza served by Straw Hat. 
(Note: The pizza need not be eaten at Straw Hat.) 

```SQL
\project_{name} (
    \select_{gender='female'} Person 
    \join Eats
    \join \select_{pizzeria='Straw Hat'} Serves 
);
```


Q3. Find all pizzerias that serve at least one pizza for less than $10 that either 
Amy or Fay (or both) eat. 

```SQL
\project_{pizzeria} (
    \select_{price <= 10}
        (\select_{name='Amy' or name='Fay'} Eats
        \join Serves)
);
```


Q4. Find all pizzerias that serve at least one pizza for less than $10 that both 
Amy and Fay eat. 

```SQL
\project_{pizzeria} (
    \select_{price <= 10} Serves
    \join
    (\project_{pizza} (\select_{name='Amy'} Eats)
    \intersect
    \project_{pizza} (\select_{name='Fay'} Eats))
);
```


Q5. Find the names of all people who eat at least one pizza served by Dominos but 
who do not frequent Dominos.

```SQL
\project_{name} (
    \select_{pizzeria = 'Dominos'} Serves \join Eats
)
\diff
\project_{name} (
    \select_{pizzeria = 'Dominos'} Frequents
);
```


Q6. Find all pizzas that are eaten only by people younger than 24, or that cost 
less than $10 everywhere they're served. 

```SQL
(\project_{pizza} Eats
 \diff
 (\project_{pizza} 
     \select_{age >= 24} (Person \join Eats)
 )
)
\union
(\project_{pizza} Serves
 \diff
 (\project_{pizza} \select_{price > 10} Serves)
);
```


Q7. Find the age of the oldest person (or people) who eat mushroom pizza.

```SQL
\project_{allAgeMushroom}(
    \rename_{allAgeMushroom}(\project_{age}(
        \project_{name}(\select_{pizza = 'mushroom'} Eats)
        \join Person
        )
    )
)
\diff
\project_{notOldestAgeMushroom}(
    \rename_{notOldestAgeMushroom}(\project_{age}(
        \project_{name}(\select_{pizza = 'mushroom'} Eats)
        \join Person
        )
    )
    \join_{notOldestAgeMushroom < allAgeMushroom}
    \rename_{allAgeMushroom}(\project_{age}(
        \project_{name}(\select_{pizza = 'mushroom'} Eats)
        \join Person
        )
    )
);
```


Q8. Find all pizzerias that serve only pizzas eaten by people over 30. 

```SQL
\project_{pizzeria}(Serves)
\diff
\project_{pizzeria}(
    Serves \join (
        \project_{pizza}(Eats)
        \diff
        \project_{pizza}(\select_{age > 30} Person \join Eats)
    )
);
```


Q9. Find all pizzerias that serve every pizza eaten by people over 30. 

```SQL
\project_{pizzeria} Serves
\diff
(\project_{pizzeria}(
    (\project_{pizza}(\select_{age > 30} Person \join Eats)
     \cross
     \project_{pizzeria}(Serves)
    )
    \diff 
    (\project_{pizza, pizzeria} Serves)
    )
);
```
================================================================================