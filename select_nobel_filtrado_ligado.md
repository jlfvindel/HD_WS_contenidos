# FILTER, BIND y funciones


## FILTER

### Efecto del filtrado
La siguiente consulta solicita 'reconocimiento, año y nombre de premiado' pero sólo de los concedidos posteriormente a 1970. Esta tarea no se puede resolver con el uso descrito para MINUS:
+ Con MINUS se podrían descartar de los resultados del patrón {#1,#2,#3} aquellos premiados para los que exista año de concesión
+ Pero no es eso lo que se pretende: interesa *entrar dentro del valor del año* y mantener sólo los resultados con valor de año superior a 1970.

Para ello se requiere FILTER, para dejar pasar tan sólo los resultados cuyos valores cumplen una determinada condición.

:::::{hint} Consulta: tan sólo los resultados con año posterior a 1970
:label: query-filtrado-simple
:class: dropdown
:open: 

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>

SELECT DISTINCT ?reconocimiento ?año ?premiado 
WHERE {
    ?reconocimiento rdf:type nobel:LaureateAward .          #1
    ?reconocimiento nobel:year ?año .                       #2
    ?reconocimiento nobel:laureate ?premiado .              #3 
    FILTER(?año > 1970)
}
ORDER BY ?año
```
Se sugiere copiar esta pregunta y comentar el filtrado (es decir, anteponer # a la línea de FILTER para que no se ejecute). Cuando se ejecuta en https://data.nobelprize.org/sparql se obtienen los resultados del patrón {#1,#2,#3} sin filtrar, de cualquier año. Si se descomenta FILTER y se ejecuta, tan sólo aparecen valores superior a 1970.
:::::


### Ámbito del filtrado
FILTER ejecuta el filtrado requerido **en el ámbito del grupo** de enunciados en que se encuentra. La siguiente consulta considera dos grupos, {#1,#2,#3} y {#4,#5}, como patrones de búsqueda. Sobre los resultados intermedios del grupo1 se aplica un determinado FILTER. Y sobre los resultados intermedios del grupo2 se aplica independientemente otro filtrado. Y posteriormente se componen ambos grupos de respuestas mediante MINUS.

:::::{hint} Consulta: dos filtros, cada uno en su ámbito de ejecución
:label: query-filtrados-dos-ambitos
:class: dropdown
:open: 

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX dbp: <http://dbpedia.org/property/>

SELECT DISTINCT ?reconocimiento ?año ?premiado 
WHERE {
  { ?reconocimiento rdf:type nobel:LaureateAward .          #1
    ?reconocimiento nobel:year ?año .                       #2
    ?reconocimiento nobel:laureate ?premiado .              #3 
    FILTER(?año > 1970) }
  MINUS 
  { ?premiado dbp:dateOfDeath ?fechaFallec .                #4
    ?premiado foaf:gender ?género .                         #5
    FILTER(?género = "male") }
}
ORDER BY ?año
```
Cuando se ejecuta en https://data.nobelprize.org/sparql se obtiene el siguente resultado: (A) por un lado, los resultados (?reconocimiento, ?año, ?premiado) con año mayor que 1970; (B) por otro lado (?premiado, ?genero) siempre que el género sea masculino. Finalmente (A-B) se descartan de los premiados en (A) aquéllos que también aparecen en (B):

En resumen, se solicita *"los (?reconocimiento,?año,?premiado) a partir de 1970 salvo los premiados que cumplan tener fecha de fallecimiento y género masculino"*. Aparecen más de trescientos resultados que pueden ser de género femenino (fallecidas o no) o de género masculino no fallecidos.
:::::

### Condiciones compuestas
La siguiente consulta consta de un único grupo de enunciados y por tanto se pueden omitir las llaves que lo enmarcan. Hay un filtro aplicado sobre sus instancias. Un filtro puede estar situado en cualquier posición entre los enunciados de su grupo: **su ejecución siempre se producirá al final** de la búsqueda del grupo, para mantener las instancias que cumplan la condición. La condición puede ser compuesta a partir de conjunciones (&&), disyunciones (||) o negaciones (!).

:::::{hint} Consulta: filtro con una condición compuesta (conjuntiva)
:label: query-filtrado-condicion-compuesta
:class: dropdown
:open:
```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT DISTINCT ?reconocimiento ?año ?premiado ?género
WHERE {
    ?reconocimiento rdf:type nobel:LaureateAward .          #1
    ?reconocimiento nobel:year ?año .                       #2
    FILTER(?año > 1970 && ?género="female")
    ?reconocimiento nobel:laureate ?premiado .              #3
    ?premiado foaf:gender ?género .                         #4
}
ORDER BY ?año ?premiado
```
:::::

### Funciones
[SPARQL facilita funciones](https://www.w3.org/TR/sparql11-query/#expressions) sobre términos RDF. Su resultado se puede utilizar en la composición de condiciones en filtros, como por ejemplo en:
+ FILTER ( LANG(?variable) = "en" ): el literal almacenado en ?variable debe ser del tipo xxxx@en, es decir, declarado como literal en inglés.
+ FILTER ( (YEAR(NOW()) - YEAR(?variable)) > 20 ): el año almacenado en ?variable está a más de veinte años del actual.
+ FILTER ( STRSTARTS(?variable) = "Priz" ): el literal almacenado en ?variable comienza por los caracteres "Priz".

Adicionalmente, FILTER (NOT) EXISTS se aplica sobre grupos de enunciados, como en el siguiente ejemplo

:::::{hint} Consulta: uso de funciones en las condiciones de los filtros
:label: query-filtrado-condicion-con-funcion
:class: dropdown
:open:

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>

SELECT DISTINCT ?reconocimiento ?motivación
WHERE {
  ?reconocimiento rdf:type nobel:LaureateAward .
  ?reconocimiento nobel:motivation ?motivación .
  FILTER( LANG(?motivación) = "en" )
  FILTER NOT EXISTS {?reconocimiento nobel:university ?universidad}
}
ORDER BY ?reconocimiento
```

Esta consulta devuelve cada reconocimiento junto al literal de su motivación (en su versión en inglés), siempre que ese reconocimiento no esté asociado a una universidad (es decir que se cumpla que no existe una terna asocia ese reconocimiento a una universidad. 

Se puede ejecutar esta consulta en https://data.nobelprize.org/sparql comentando (es decir, 'desconectando') los dos filtros o sólo uno de ellos o ninguno.
:::::

## BIND
`BIND(valor AS ?variable)` liga ese 'valor' concreto como valor fijo de la ?variable, para toda búsqueda de instancias de ese patrón. En la siguiente consulta se usa para mostrar en la variable ?edadPremiado la edad que tenía el premiado cuando se le asignó el premio Nobel.
+ `BIND ( (?añoNobel - YEAR(?fechaNac)) AS ?edadPremiado )` calcula primero la diferencia entre el año de concesión (?añoNobel) y el año extraído de la fecha de nacimiento, y esta edad calculada del premiado en la fecha de concesión se almacena en la variable ?edadPremiado.

:::::{hint} Consulta: asignación de un valor a un varible en WHERE mediante BIND
:label: query-bind-en-where
:class: dropdown
:open:

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>
PREFIX dbp: <http://dbpedia.org/property/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT DISTINCT ?premiado ?nombre ?fechaNac  ?añoNobel ?edadPremiado
WHERE {
  ?premiado rdf:type nobel:Laureate .
  ?premiado dbp:dateOfBirth ?fechaNac .
  ?premiado foaf:name ?nombre .
  ?premiado nobel:laureateAward ?reconocimiento .
  ?distinciónIndiv nobel:year ?añoNobel .
  BIND ( (?añoNobel - YEAR(?fechaNac)) AS ?edadPremiado )
}
ORDER BY ?añoNobel
```
Se recomienda ejecutar esta consulta en https://data.nobelprize.org/sparql para analizar los resultados.
:::::

El mismo resultado Se puede obtener asignando ese cálculo de edad directamente a la variable ?edadPremiado **en SELECT**, donde no se utiliza sintácticamente BIND.

:::::{hint} Consulta: asignación de un valor a un varible en SELECT
:label: query-asignacion-en-select
:class: dropdown
:open:

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>
PREFIX dbp: <http://dbpedia.org/property/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT DISTINCT ?premiado ?nombre ?fechaNac  ?añoNobel ((?añoNobel - YEAR(?fechaNac)) AS ?edadPremiado )
WHERE {
  ?premiado rdf:type nobel:Laureate .
  ?premiado dbp:dateOfBirth ?fechaNac .
  ?premiado foaf:name ?nombre .
  ?premiado nobel:laureateAward ?distinciónIndiv .
  ?distinciónIndiv nobel:year ?añoNobel .
}
ORDER BY ?añoNobel
```
Como en el resto de propuestas, se recomienda ejecutar esta consulta en https://data.nobelprize.org/sparql e incluso experimentar con modificaciones de la misma.
:::::
