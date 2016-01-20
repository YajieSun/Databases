XML World-Countries XPath and XQuery Exercises
======================================================
Q1. Return the area of Mongolia. 

```XQuery
doc("countries.xml")//country[@name = "Mongolia"]/data(@area)
```


Q2. Return the names of all cities that have the same name as the country in which they are located. 

```XQuery
for $country in doc("countries.xml")//country
for $city in $country/city
where $city/name = $country/@name
return $city/name
```


Q3. Return the average population of Russian-speaking countries. 

```XQuery
avg(for $country in doc("countries.xml")//country
    where $country//language = "Russian"
    return $country/data(@population))
```


Q4. Return the names of all countries that have at least three cities with population greater than 3 million. 

```XQuery
doc("countries.xml")//country[count(city[population > 3000000]) >= 3]/data(@name)
```


Q5. Create a list of French-speaking and German-speaking countries. The result should take the form:
<result>
  <French>
    <country>country-name</country>
    <country>country-name</country>
    ...
  </French>
  <German>
    <country>country-name</country>
    <country>country-name</country>
    ...
  </German>
</result>

```XQuery
<result>
    <French>
    {
        for $country in doc("countries.xml")//country
        where $country//language = "French"
        return <country> {$country/data(@name)} </country>
    }
    </French>
    <German>
    {
        for $country in doc("countries.xml")//country
        where $country//language = "German"
        return <country> {$country/data(@name)} </country>
    }
    </German>
</result>
```


Q6. Return the countries with the highest and lowest population densities. Note that because the "/" operator has its own meaning in XPath and XQuery, the division operator is infix "div". To compute population density use "(@population div @area)". You can assume density values are unique. The result should take the form:
<result>
  <highest density="value">country-name</highest>
  <lowest density="value">country-name</lowest>
</result>

```XQuery
<result>
  <highest density = "{max(doc("countries.xml")//country/(@population div @area))}">
  {
      for $country in doc("countries.xml")//country
      where data($country/(@population div @area)) = max(doc("countries.xml")//country/(@population div @area))
      return data($country/@name)
  }
  </highest>
  <lowest density = "{min(doc("countries.xml")//country/(@population div @area))}">
  {
      for $country in doc("countries.xml")//country
      where data($country/(@population div @area)) = min(doc("countries.xml")//country/(@population div @area))
      return data($country/@name)
  }
  </lowest>
</result>
```
======================================================