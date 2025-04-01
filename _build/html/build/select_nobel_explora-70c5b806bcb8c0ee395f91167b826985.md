# Consultas exploratorias
Cuando no se conoce la estructura de un repositorio RDF se puede tantear con  algunas consultas generales, aplicables a cualquier dataset RDF, que proporcionan información estructural.


## Individuos, clases y propiedades
La primera consulta detecta todas las clases utilizadas en el repositorio. La segunda, todas las propiedades utilizadas. Aprovechando esta información, se presenta una consulta que demanda todos los elementos de una de las clases detectadas.

### Detección de todas las clases
La siguiente consulta devuelve todas las clases del repositorio. Se puede copiar, pegar y ejecutar en la página [https://data.nobelprize.org/sparql](https://data.nobelprize.org/sparql).

:::::{hint} Consulta: todas las clases del dataset
:label: query-nobel-todas-clases
:class: dropdown
:open:

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT DISTINCT ?clase         #resultado requerido
WHERE {
  ?elemento rdf:type ?clase    #patrón de búsqueda
}
```
:::::

:::::{admonition} Estructura de la consulta
:class: dropdown
:open:

**Patrón de búsqueda**. El patrón de búsqueda se sitúa en el cuerpo de WHERE. En este caso consta de una única tripleta. El literal que sigue al signo # es un comentario opcional no ejecutable. En la tripleta de búsqueda hay dos variables. Cada una de ellas comienza por ? y **se les puede asignar el nombre que se desee**. 

**Variables y concordancias detectadas**. Este patrón recorre todos los enunciados del dataset y detecta los que son del tipo "algo1 **rdf:type** algo2". Si se aplicara sobre los enunciados de la siguiente tabla izquierda se detectaría concordancia en tres de ellos. Estas concordancias se van almacenando en una tabla interna (?elemento, ?clase) como la que se muestra a la derecha.

:::{math}
:enumerated: false
\begin{array}{c}
\text{tripletas en el dataset} \\ \hline
<sujetoA> <rdf:type> <clase1> \\
<sujetoB><(otro\ predicado)><objetoK>\\
<sujetoC><rdf:type><clase2> \\
<sujetoD><rdf:type><clase1> 
\end{array}\quad \Rightarrow 
\begin{array}{cc}
?elemento  & ?clase \\ \hline
sujetoA &  clase1 \\
 &   \\
sujetoC & clase2 \\
sujetoD & clase1 
\end{array}
:::

**Resultado final deseado**. En SELECT se expresa qué resultados se desea construir a partir de la tabla interna anterior. En este caso, sólo interesan los valores detectados en la columna ?clase de esa tabla interna. Y además interesan sin repeticiones (DISTINCT). Es decir, a partir de la tabla interna de la explicación anterior se produciría como resultado de SELECT:

:::{math}
:enumerated: false
\begin{array}{c}
?clase \\ \hline
clase1 \\
clase2 \\
\end{array}
:::
:::::

:::{admonition} Resultados
:class: dropdown
:open:
El punto de consulta de la Fundación Nobel devuelve una relación completa de sus clases. Tres de ellas son propias. Corresponden a las categorías escogidas para estructurar su información específica.

+ `<http://data.nobelprize.org/terms/Laureate>`
+ `<http://data.nobelprize.org/terms/LaureateAward>`
+ `<http://data.nobelprize.org/terms/NobelPrize>`

Aparte de estas tres clases, este repositorio 'toma prestados' identificadores de clases de otras entidades. En particular, va a utilizar los identificadores (y exactamente la misma semántica) que utiliza [dbpedia.org](https://www.dbpedia.org) para designar los siguientes conceptos *Award*, *Country*, *City* y *University*.

Del estándar [foaf](http://xmlns.com/foaf/spec/) (Friend of a friend) utiliza sus identificadores para *Person*, *Organization* y *Agent*.
:::

### Detección de las propiedades
La siguiente consulta demanda todas las propiedades del dataset. Se puede copiar, pegar y ejecutar en la página [https://data.nobelprize.org/sparql](https://data.nobelprize.org/sparql).


:::{hint} Consulta: todas las propiedades del dataset
:label: query-nobel-todas-propiedades
:class: dropdown
:open:

```SPARQL
# No se especifican prefijos porque no se usan en la consulta

SELECT DISTINCT ?propiedad
WHERE {
  ?sujeto ?propiedad ?objeto
}
```
:::

:::{admonition} Estructura de la consulta
:class: dropdown
:open:

El patrón de búsqueda (en WHERE) concuerda con todos y cada uno de los enunciados del dataset porque es del tipo: "algo1 con_alguna_relación_con algo2". De esta forma, cuando el patrón recorre los enunciados del dataset va incorporando cada uno de ellos a la tabla interna de concordancias.

Esta tabla de concordancias consta de tres columnas (?sujeto, ?propiedad, ?objeto) y tiene en sus filas todos los enunciados del dataset (porque todos concuerdan con el patrón).

En SELECT se especifica que tan sólo se quieren resultados de la columna ?propiedad de esa tabla interna generada. Y además, se solicita (mediante DISTINCT) que se evite mencionar repetidamente una propiedad aunque aparezca en varias filas de la tabla. 

El resultado final es la relación de todas las propiedades del dataset: no porque hayan sido declaradas explícitamente como tales sino porque son términos que aparecen en segundo lugar en los enunciados.
:::

:::{admonition} Resultados
:class: dropdown
:open:
Se obtiene una relación de más de cincuenta propiedades utilizadas internamente. Las propiedades propias, que la Fundación Nobel ha considerado que debía usar, con su semántica específica, son:

+ `<http://data.nobelprize.org/terms/laureate>`
+ `<http://data.nobelprize.org/terms/laureateAward>`
+ `<http://data.nobelprize.org/terms/nobelPrize>`
+ `<http://data.nobelprize.org/terms/share>`
+ `<http://data.nobelprize.org/terms/sortOrder>`
+ `<http://data.nobelprize.org/terms/motivation>`
+ `<http://data.nobelprize.org/terms/year>`
+ `<http://data.nobelprize.org/terms/category>`
+ `<http://data.nobelprize.org/terms/categoryOrder>`
+ `<http://data.nobelprize.org/terms/university>`

Adicionalmente hay otras propiedades definidas por otras instituciones que discrecionalmente se usan en este dataset porque están siendo usadas tal cual en muchos otros repositorios públicos.
:::

### Todos los individuos de una clase
Se detectó que una de las clases del vocabulario era `NobelPrize`. La siguiente consulta obtiene la enumeración completa de todos los elementos de esa clase. De nuevo, se puede copiar, pegar y ejecutar en [https://data.nobelprize.org/sparql](https://data.nobelprize.org/sparql).

:::{hint} Consulta: todos los elementos de la clase NobelPrize
:label: query-nobel-todos-en-clase-nobelprize
:class: dropdown
:open:

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>

SELECT ?premioNobel
WHERE {
  ?premioNobel rdf:type nobel:NobelPrize
}
ORDER BY ?premioNobel
LIMIT 100000
```
:::

:::{admonition} Estructura de la consulta
:class: dropdown
:open:

El patrón de consulta es "*algo* pertenece_a clase_NobelPrize". Concuerda con en cada caso en que un elemento concreto es sujeto de un enunciado así y almacena internamente cada uno de esos elementos en una tabla interna con una única columna (?premioNobel). 

Fuera del patrón de búsqueda (fuera de WHERE) se solicita que esa tabla interna de concordancias presente ordenadas sus filas por la columna (?premioNobel), que coincide aquí en ser la única columna. Y además, que a lo sumo acumule 100000 concordancias, es decir, que pare la búsqueda cuando llegue a ese límite.

El uso de LIMIT es una buena práctica porque algunas consultas en ciertos repositorios pueden retornar millones de resultados y el servidor puede interrumpir la ejecuciónde nuestra consulta por exceso de tiempo consumido. 

**Solicitar tan sólo el número de resultados**. Como opción alternativa, se puede solicitar tan sólo el número de resultados. En la siguiente consulta lo que se almacena internamente es un contador de concordancias de la variable ?premioNobel como sujeto de ese patrón. Y en SELECT se solicita su suma y el almacenamiento del resultado en la variable ?premioNobelNum, que es el resultado final de la consulta. 

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX nobel: <http://data.nobelprize.org/terms/>

SELECT (COUNT(?premioNobel) AS ?premioNobelNum)
WHERE {?premioNobel rdf:type nobel:NobelPrize}
```
:::

:::{admonition} Resultados
:class: dropdown
:open:
Cuando se pregunta por los elementos que pertenecen a la clase NobelPrize se obtiene más de mil docientos identificadores de eso elementos similares al siguiente:

+ `<http://data.nobelprize.org/resource/nobelprize/Chemistry/1901>`

Cada año se concede un único premio Nobel por cada categoría, que puede recaer de forma compartida en uno o más premiados (hasta un total de 3) que pueden ser personas o instituciones.
:::

### Descarga de todos los enunciados
No es lo habitual en los dataset públicos, pero el conjunto de datos de este repositorio es suficientemente pequeño como para plantearse recuperarlo en su totalidad. Como medida de precaución la consulta se autoimpone un límite, que se puede ir ampliando. 

:::::{hint} Consulta: todos los enunciados del dataset consultado
:label: query-nobel-todos-enunciados
:class: dropdown
:open:

```SPARQL
SELECT DISTINCT ?sujeto ?propiedad ?objeto
WHERE { ?sujeto ?propiedad ?objeto }
ORDER BY ?propiedad
LIMIT 300000
```
:::::

:::{admonition} Estructura de la consulta
:class: dropdown
:open:
Esta consulta tiene el mismo patrón de búsqueda que la realizada [para extraer todas las propiedades](#query-nobel-todas-propiedades): "algo1 tiene_alguna_relación_con algo2". En ambas consultas la tabla interna de concordancias almacena cada enunciado del dataset debajo de las columnas (?sujeto, ?predicado, ?objeto). Pero en la consulta sobre propiedades se pedía sólo la columna intermedia sin repeticiones y en esta consulta se piden las 3 columnas (es decir, el enunciado completo).
:::

:::{admonition} Resultados
:class: dropdown
:open:
Cuando se ejecuta, el sistema muestra más de cuarenta y siete mil filas (?sujeto, ?predicado, ?objeto), correspondientes a los enunciados del dataset.
:::

:::{important} Sobre la descarga de todos los enunciados en Nobelprizes
El punto de consulta de la Fundación Nobel permite descargar la tabla de resultados devueltos para cualquier consulta. Se recomienda descargar los resultados de la consulta anterior.

Esta tabla de resultados concreta no es estructuralmente un volcado de la base de datos: no está expresada en un formato RDF y no se puede importar tal cual en un gestor de ternas RDF. En general, las consultas SELECT no retornan un conjunto de tripletas RDF sino una 'tabla de filas con N columnas'. En concreto, cada fila consta de los N valores de cada concordancia del patrón para las variables (?var1, ..., ?varN) solicitadas en SELECT. 

No obstante, si esta descarga se produce en formato CSV y se abre este fichero se observan resultados como los siguientes:
```csv
"sujeto","propiedad","objeto"
"http://data.nobelprize.org/resource/laureateaward/Chemistry/1901/160","http://data.nobelprize.org/terms/category","http://data.nobelprize.org/resource/category/Chemistry"
"http://data.nobelprize.org/resource/laureateaward/Chemistry/1902/161","http://data.nobelprize.org/terms/category","http://data.nobelprize.org/resource/category/Chemistry"
...
```
Es fácil programar un script Python (o solicitarlo a ChatGPT o servicio similar) para convertir cada línea del CSV en un enunciado RDF correctamente expresado en formato N-Triples. 

Y este fichero (o su conversión a cualquier formato RDF) **sí es consultable localmente en SPARQL** desde Python o tras su importación en una base de datos local como GraphDB Desktop.
:::