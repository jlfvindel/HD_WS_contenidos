# Agregación por categorías
El conjunto de resultados devuelto por los patrones de búsqueda **se pueden partir en clases disjuntas**. Esto permite ejecutar algunos cálculos sobre los elementos agregados de cada clase.

## Cálculos sobre una categoría
La siguiente consulta mantiene todos los resultados en un misma clase y tan sólo devuelve un número: **el recuento de los premiados que tenían menos de 50 años** en la fecha de concesión de su premio Nobel. La función COUNT actúa sobre todas las instancias devueltas de la variable ?premiado y presenta su resultado en una variable nueva, como es ?numPremiado.

:::{hint} Consulta: recuento de premiados menores de 50
:label: query-recuento
:class: dropdown
:open: 

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>
PREFIX dbp: <http://dbpedia.org/property/>

SELECT (COUNT(DISTINCT ?premiado) AS ?numPremiado)
WHERE {
  ?premiado rdf:type nobel:Laureate .
  ?premiado dbp:dateOfBirth ?fechaNac .
  ?premiado nobel:laureateAward ?distinciónIndiv .
  ?distinciónIndiv nobel:year ?añoNobel .
  BIND ( (?añoNobel - year(?fechaNac)) AS ?edadNobel)
  FILTER ( ?edadNobel < 50 )
}
```
[Punto de consulta](https://data.nobelprize.org/sparql)
:::::

:::{hint} Consulta: edad promedio en la concesión del Nobel
:label: query-edad-promedio-concesion
:class: dropdown
:open: 
Si las instancias devueltas son números, se puede calcular su máximo, mínimo o promedio. Esta consulta devuelve la edad promedio que tenían los premiados Nobel en la fecha de su concesión.

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>
PREFIX dbp: <http://dbpedia.org/property/>

SELECT (AVG(?edadNobel) AS ?edadNobelPromedio)
WHERE {
  ?premiado rdf:type nobel:Laureate .
  ?premiado dbp:dateOfBirth ?fechaNac .
  ?premiado nobel:laureateAward ?distinciónIndiv .
  ?distinciónIndiv nobel:year ?añoNobel .
  BIND ( (?añoNobel - year(?fechaNac)) AS ?edadNobel)
}
```
[Punto de consulta](https://data.nobelprize.org/sparql)
:::::

## Agrupación por categorías

En la siguiente consulta, el patrón de búsqueda devuelve valores para las variables (?premioNobel, ?premiado, ?categoría). La instrucción GROUP BY produce una partición de estas instancias: **pertenecen al mismo agrupamiento aquellas instancias que coincidan en tener el mismo valor** para la variable escogida. 

Dentro de cada agrupamiento, sobre los valores del resto de variables, se puede ejecutar un cálculo agregado. La siguiente consulta devuelve los diversos tipos de premios Nobel y, dentro de cada uno, el número de premiados.

:::{hint} Consulta: recuento de premiados por cada una de las distintas categorías
:label: query-recuento-por-categoria
:class: dropdown
:open: 
```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>

SELECT ?categoría  (COUNT(DISTINCT ?premiado) AS ?numPremiadosCategoría)
WHERE {
  ?premioNobel rdf:type nobel:NobelPrize .
  ?premioNobel nobel:laureate ?premiado .
  ?premioNobel nobel:category ?categoría .
}
GROUP BY ?categoría 
```
[Punto de consulta](https://data.nobelprize.org/sparql)
:::::

## Categorías y subcategorías
En la siguiente consulta, WHERE devuelve instancias para la n-tupla de variables (?premioNobel, ?categoría, ?premiado, ?género). Y GROUP BY especifica **un agrupamiento** entre las instancias con el mismo valor para ?categoría **y, dentro de cada grupo, una nueva partición** entre las instancias con el mismo valor para ?género.

Cada uno de estos grupos está compuesto por las distintas instancias (?premioNobel, ?categoría=X, ?premiado, ?género=Y), con X e Y fijas para cada grupo. Los cálculos sobre agregados se pueden realizar ahora dentro de cada grupo sobre los valores de (?premioNobel, ?premiado). La siguiente consulta devuelve los tipos de premios Nobel, diferenciando adicionalmente por género, y en cada apartado se añade su número de premiados.

:::{hint} Consulta: recuento de premiados por categoría y genéro
:label: query-recuento-por-categoria-genero
:class: dropdown
:open:
```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?categoría ?género (COUNT(DISTINCT ?premiado) AS ?numPremiadosGrupo)
WHERE {
  ?premioNobel rdf:type nobel:NobelPrize .
  ?premioNobel nobel:category ?categoría .
  ?premioNobel nobel:laureate ?premiado .
  ?premiado foaf:gender ?género .
  
}
GROUP BY ?categoría ?género
ORDER BY ?categoría ?género
```
[Punto de consulta](https://data.nobelprize.org/sparql)
:::::

## Filtrado de categorías
Cuando se produce una partición mediante el uso de GROUP BY **quizá no interese mantener el interés sobre todos los grupos detectados**. Se puede aplicar a estos grupos el concepto de filtrado ya visto con anterioridad y descartar algunos grupos que no cumplen cierta condición. Sintácticamente, en este punto, se usa **HAVING en vez de FILTER** para mantener los grupos que cumplen la condición requerida.

La siguiente consulta agrupa por categoría y genéro, pero tan sólo mantiene aquellos grupos donde la edad mínima a la hora de recibir el premio era mayor de 36. (Es decir, quedan descartados los grupos en que algún premiado tenía menos o igual que 36 años al recibir el premio). De cada uno de estos grupos donde la condición se cumple adicionalmente se calcula en SELECT el recuento de sus premiados.

:::{hint} Consulta: recuento de premiados por categoría y genéro sólo para ciertos grupos
:label: query-recuento-por-categoria-genero-filtrado-grupos
:class: dropdown
:open:
```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX dbp: <http://dbpedia.org/property/>

SELECT ?categoría ?género (COUNT(DISTINCT ?premiado) AS ?numPremiadosGrupo)
WHERE {
  ?premioNobel rdf:type nobel:NobelPrize .
  ?premioNobel nobel:category ?categoría .
  ?premioNobel nobel:year ?añoNobel .
  ?premioNobel nobel:laureate ?premiado .
  ?premiado foaf:gender ?género .
  ?premiado dbp:dateOfBirth ?fechaNacim .
  BIND ((?añoNobel - YEAR(?fechaNacim)) AS ?edadPremiado) .
}
GROUP BY ?categoría ?género HAVING (MIN(?edadPremiado) > 36 )
ORDER BY ?categoría ?género
```
Sobre esta pregunta se sugiere ir variando el umbral de edad mínima de premiados y observar los grupos que aún cumplen la condicion.

[Punto de consulta](https://data.nobelprize.org/sparql)
:::::