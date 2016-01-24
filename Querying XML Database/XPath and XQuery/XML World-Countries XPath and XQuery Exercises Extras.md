XML World-Countries XPath and XQuery Exercises Extras
===========================================================
Q1. Return the names of all countries with population greater than 100 million. 

```XQuery
doc("countries.xml")//country[@population > 100000000]/data(@name)
```


Q2. Return the names of all countries where over 50% of the population speaks German. (Hint: Depending on your solution, you may want to use ".", which refers to the "current element" within an XPath expression.) 

```XQuery
doc("countries.xml")//country[language[@percentage > 50] = 'German']/data(@name)
```


Q3. Return the names of all countries where a city in that country contains more than one-third of the country's population. 

```XQuery
for $country in doc("countries.xml")//country
    for $city in $country/city
    where $country/data(@population) div $city/population < 3 
    return $country/data(@name)
```


Q4. Return the population density of Qatar. Note: Since the "/" operator has its own meaning in XPath and XQuery, the division operator is "div". To compute population density use "(@population div @area)". 

```XQuery
for $country in doc("countries.xml")//country
    where $country/data(@name) = "Qatar"
    return $country/data(@population) div $country/data(@area)
```


Q5. Return the names of all countries whose population is less than one thousandth that of some city (in any country). 

```XQuery
let $maxpopulation := max(doc("countries.xml")//city/population)

for $country in doc("countries.xml")//country
where $maxpopulation div $country/data(@population) > 1000
return $country/data(@name)
```


Q6. Return all city names that appear more than once, i.e., there is more than one city with that name in the data. Return only one instance of each such city name. (Hint: You might want to use the "preceding" and/or "following" navigation axes for this query, which were not covered in the video or our demo script; they match any preceding or following node, not just siblings.) 

```XQuery
for $city in doc("countries.xml")//city
where $city/name = $city/preceding::city/name
return $city/name
```


Q7. Return the names of all countries containing a city such that some other country has a city of the same name. (Hint: You might want to use the "preceding" and/or "following" navigation axes for this query, which were not covered in the video or our demo script; they match any preceding or following node, not just siblings.) 

```XQuery
for $city in doc("countries.xml")//city
where $city/name = $city/preceding::city/name or $city/name = $city/following::city/name 
return $city/parent::country/data(@name)
```


Q8. Return the names of all countries whose name textually contains a language spoken in that country. For instance, Uzbek is spoken in Uzbekistan, so return Uzbekistan. (Hint: You may want to use ".", which refers to the "current element" within an XPath expression.)

```XQuery
for $country in doc("countries.xml")//country
where some $language in $country/language satisfies contains($country/@name, $language) 
return $country/data(@name)
```


Q9. Return the names of all countries in which people speak a language whose name textually contains the name of the country. For instance, Japanese is spoken in Japan, so return Japan. (Hint: You may want to use ".", which refers to the "current element" within an XPath expression.) 

```XQuery
for $country in doc("countries.xml")//country
where some $language in $country/language satisfies contains($language, $country/@name) 
return $country/data(@name)
```


Q10. Return all languages spoken in a country whose name textually contains the language name. For instance, German is spoken in Germany, so return German. (Hint: Depending on your solution, may want to use data(.), which returns the text value of the "current element" within an XPath expression.) 

```XQuery
for $language in doc("countries.xml")//language
where contains($language/parent::country/@name, $language) 
return $language/data(.)
```


Q11. Return all languages whose name textually contains the name of a country in which the language is spoken. For instance, Icelandic is spoken in Iceland, so return Icelandic. (Hint: Depending on your solution, may want to use data(.), which returns the text value of the "current element" within an XPath expression.) 

```XQuery
for $language in doc("countries.xml")//language
where contains($language, $language/parent::country/@name) 
return $language/data(.)
```


Q12. Return the number of countries where Russian is spoken. 

```XQuery
count(for $country in doc("countries.xml")//country
      where some $language in $country/language satisfies $language/data(.) = "Russian"
      return $country)
```


Q13. Return the names of all countries for which the data does not include any languages or cities, but the country has more than 10 million people. 

```XQuery
doc("countries.xml")//country[count(city) = 0 and count(language) = 0 and @population > 10000000]/data(@name)
```


Q14. Return the name of the country with the highest population. (Hint: You may need to explicitly cast population numbers as integers with xs:int() to get the correct answer.) 


```XQuery
doc("countries.xml")//country[@population = max(doc("countries.xml")//country/@population)]/data(@name)
```


Q15. Return the name of the country that has the city with the highest population. (Hint: You may need to explicitly cast population numbers as integers with xs:int() to get the correct answer.) 

```XQuery
for $city in doc("countries.xml")//city
where $city/population = max(doc("countries.xml")//city/population)
return $city/parent::country/data(@name)
```


Q16. Return the average number of languages spoken in countries where Russian is spoken. 

```XQuery
avg(for $country in doc("countries.xml")//country
    where some $language in $country/language satisfies $language/data(.) = "Russian"
    return count($country/language))
```


Q17. Return all country-language pairs where the language is spoken in the country and the name of the country textually contains the language name. Return each pair as a country element with language attribute, e.g.,
<country language="French">French Guiana</country>

```XQuery
for $country in doc("countries.xml")//country
    for $language in $country/language
    where contains($country/@name, $language) 
    return <country language=" {data($language)} ">
           { $country/data(@name) }
           </country>
```


Q18. Return all countries that have at least one city with population greater than 7 million. For each one, return the country name along with the cities greater than 7 million, in the format:
<country name="country-name">
  <big>city-name</big>
  <big>city-name</big>
  ...
</country>

```XQuery
for $country in doc("countries.xml")//country
where some $city in $country/city satisfies $city[population > 7000000]
return <country name="{ $country/data(@name) }">
       {
           for $c in $country/city[population > 7000000]
               return <big> { $c/data(name) } </big>
       }
       </country>
```



Q19. Return all countries where at least one language is listed, but the total percentage for all listed languages is less than 90%. Return the country element with its name attribute and its language subelements, but no other attributes or subelements. 

```XQuery
for $country in doc("countries.xml")//country[count(language) > 0]
where $country[language][sum(language/@percentage) < 90]
return <country name=" { $country/data(@name) } ">
       {
           for $language in $country/language
               return $language
       }
       </country>
```


Q20. Return all countries where at least one language is listed, and every listed language is spoken by less than 20% of the population. Return the country element with its name attribute and its language subelements, but no other attributes or subelements. 

```XQuery
for $country in doc("countries.xml")//country[count(language) > 0]
where every $language in $country/language satisfies $language/data(@percentage) < 20 
return <country name="{ $country/data(@name) }">
       {
           for $language in $country/language 
               return $language
       }
       </country>
```


Q21. Find all situations where one country's most popular language is another country's least popular, and both countries list more than one language. (Hint: You may need to explicitly cast percentages as floating-point numbers with xs:float() to get the correct answer.) Return the name of the language and the two countries, each in the format:
<LangPair language="lang-name">
  <MostPopular>country-name</MostPopular>
  <LeastPopular>country-name</LeastPopular>
</LangPair>

```XQuery
let $MostPopular := for $country in doc("countries.xml")//country[count(language) > 1]
                    for $language in $country/language
                    where xs:float($language/data(@percentage)) = xs:float(max($country/language/data(@percentage)))
                    return $language
let $LeastPopular := for $country in doc("countries.xml")//country[count(language) > 1]
                     for $language in $country/language
                     where xs:float($language/data(@percentage)) = xs:float(min($country/language/data(@percentage)))
                     return $language
for $m in $MostPopular
for $l in $LeastPopular
where data($m) = data($l)
return <LangPair language="{ data($m) }">
           <MostPopular>{ $m/parent::country/data(@name) }</MostPopular>
           <LeastPopular>{ $l/parent::country/data(@name) }</LeastPopular>
       </LangPair>
```


Q22. For each language spoken in one or more countries, create a "language" element with a "name" attribute and one "country" subelement for each country in which the language is spoken. The "country" subelements should have two attributes: the country "name", and "speakers" containing the number of speakers of that language (based on language percentage and the country's population). Order the result by language name, and enclose the entire list in a single "languages" element. For example, your result might look like:
<languages>
  ...
  <language name="Arabic">
    <country name="Iran" speakers="660942"/>
    <country name="Saudi Arabia" speakers="19409058"/>
    <country name="Yemen" speakers="13483178"/>
  </language>
  ...
</languages>
(This problem is very challenging; extra congratulations if you get it right.) 

```XQuery
let $languagesNameValues := distinct-values(doc("countries.xml")//language)
return <languages>
       {
           for $languagesNameValue in $languagesNameValues
           order by $languagesNameValue
           return <language name="{$languagesNameValue}">
                  {
                      for $language in doc("countries.xml")//language
                      let $speakers := xs:int($language/parent::country/@population * $language/(@percentage div 100))
                      let $countryName := $language/parent::country/data(@name)
                      where data($language) = $languagesNameValue
                      return <country name="{$countryName}" speakers="{ $speakers }"/>
                  }
                  </language>
       }
       </languages>
```