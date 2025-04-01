# Página sobre cada ítem
Cada ítem en Wikidata es sujeto en diversas tripletas que lo describen. 
+ Todos estos enunciados se pueden recopilar, para un ítem dado, **mediante una consulta** SPARQL. 
+ Wikidata mantiene para cada ítem **adicionalmente un fichero específico** que recopila todas esas tripletas. 

En este caso último caso, basta demandar al servidor de Wikidata la descarga de ese fichero, sin necesidad de una consulta SPARQL interpuesta. De hecho, hay varias versiones de esta recopilación, en forma de ficheros separados, que muestran la misma información sobre el ítem expresada en formatos distintos: *html*, *n-triples*, *turtle*, *json* o *rdf/xml*.


## Consultas SPARQL sobre un ítem
La siguiente consulta solicita cada tripleta *(sujeto, propiedad, objeto)* con sujeto wd:Q142. Como resultado se devuelven:
+ Todas las ternas con ese ítem como sujeto : `<Q142><p><o>`
+ Y esto incluye el inicio de los enunciados indirectos, es decir, el enlace del sujeto con los nodos-statement de los enunciados indirectos, sin incluir su contenido: `<Q142><p><_:stmtH>.`

:::::{hint} Consulta: todas las tripletas con Q142 como sujeto
:label: query-todos-enunciados-sobre-item
:class: dropdown
:open: 
```SPARQL
SELECT ?s ?p ?o
WHERE {
  BIND (wd:Q142 AS ?s)
  ?s ?p ?o .
}    
```    
[Ejecución en Wikidata](https://w.wiki/6ZCs)
:::::

## Página html dedicada a un ítem
Cualquier Q-ítem tiene un identificador en el mismo espacio de nombres (wd). En el caso de `wd:Q142`, el identificador es la URL: [http://www.wikidata.org/entity/Q142](http://www.wikidata.org/entity/Q142).

También las propiedades, cuando se consideran como un ítem describible (como sujeto de enunciados que la describen) tienen su identificador en ese espacio de nombres. Por ejemplo, `wd:P6`, con identificador: [http://www.wikidata.org/entity/P6](http://www.wikidata.org/entity/P6).

```{admonition} Página html informativa sobre un ítem
:class: important
Las página de un ítem muestra de manera informal las tripletas asociadas a ese ítem.

+ Se asume que las tripletas mostradas informalmente tienen por sujeto el ítem que encabeza la página.

+ Cada una de estas páginas constan de cuatro secciones de enunciados sobre el ítem: (1) etiquetas, (2) enunciados, (3) identificadores externos y (4) enlaces internos en Wikimedia.

+ Cada una de estas secciones presentan sus filas en dos columnas, col1 y col2, respectivamente con la propiedad y el objeto asociados al item de la página: `<item><valor_col1><valor_col2>`.
```

Estas páginas html sobre ítems son navegables entre sí:

+ En estas páginas, cuando se sitúa el cursor sobre un literal marcado como enlace, por ejemplo *"instance of"* o *"France"*, aparece el identificador abstracto de ese ítem o propiedad (P31 o Q142 en este ejemplo). 

+ Pulsando cualquiera de esos enlaces se llega a la página html de ese ítem. Es decir, la navegación entre conceptos y propiedades relacionados en Wikidata se facilita sobre estas páginas html.


### Sección de etiquetas
De cada entidad descrita, como  por ejemplo de la propiedad P6, se muestra una etiqueta principal, una descripción y quizá varias etiquetas alternativas, todo ello en cada uno de los idiomas en que esta información se haya registrado. La página html muestra una tabla resumen de estas etiquetas. 

Esto se puede apreciar en la página html de esta propiedad P6 considerada como un ítem. A esta página redirige Wikidata el enlace cuando se demanda al servidor el identificador de P6 (wd:P6, es decir, http://www.wikidata.org/entity/P6) y la página explicativa a la que se llega en la redirección es https://www.wikidata.org/wiki/Property:P6.

Como medio complementario de información, la siguiente consulta muestra todas las etiquetas alternativas de P6 en español.

:::::{hint} Consulta: todas las etiquetas alternativas en español de la propiedad P6
:label: query-todas-etiquetas-español
:class: dropdown
:open: 

```SPARQL
SELECT ?s ?sEtiqAlternativa
WHERE {
  BIND (wd:P6 AS ?s)
  ?s skos:altLabel ?sEtiqAlternativa .
  FILTER(LANG(?sEtiqAlternativa)="es")     #1
}
```
[Ejecución en Wikidata](https://w.wiki/6f2K)
:::::

Si se comenta la línea #1 (anteponiendo un símbolo #), al eliminar el filtrado por idioma, se obtendrían todas las etiquetas alternativas de la entidad en cualquiera de los idiomas en que existan.

### Sección de enunciados
Esta sección aparece bajo la cabecera 'Statements' si el lenguaje de presentación de la página se mantiene en inglés. De nuevo aparece una presentación tabular en html como resumen de las ternas sobre la entidad. 

Esta tabla muestra la recopilación de enunciados indirectos tal y como se produce en el interfaz gráfico de edición de enunciados, que se describe en la página [https://www.wikidata.org/wiki/Help:Statements](https://www.wikidata.org/wiki/Help:Statements).

**Enunciados directos (exportados)**. Generalmente cada propiedad que aparece en la primera columna de esta tabla exporta todos sus valores principales en forma de enunciados directos (sin modificadores ni referencias añadidas). Es decir, usualmente para muchas tareas de información, basta hacer consultas SPARQL sobre estos enunciados directos.

La página html sobre un ítem no precisa qué valores se han exportado hacia enunciados directos. La siguiente consulta recupera todos los enunciados de un ítem concreto, en particular todos los enunciados `<wd:Q142><wdt:Pxxx><o>` registrados. 

:::::{hint} Consulta: sobre Q142, todas sus tripletas `<wd:Q142><wdt:Pxxx><o>`
:label: query-todas-wdtPxxx-sobre-item
:class: dropdown
:open: 
Esta es no es una consulta adecuada a una primera aproximación. Se recomienda ejecutarla y leer con cuidado las explicaciones adjuntas.
```SPARQL
SELECT ?sujeto ?sujetoLabel ?propDirecta ?propLabel ?objeto ?objetoLabel
WHERE {
  BIND (wd:Q142 AS ?sujeto)
  ?prop rdf:type wikibase:Property .                # 1
  ?prop wikibase:directClaim ?propDirecta .         # 2
  ?sujeto ?propDirecta ?objeto .                    # 3
  SERVICE wikibase:label { bd:serviceParam wikibase:language "es,en". }
}
ORDER BY ?propDirecta
```
[Ejecución en Wikidata](https://w.wiki/Daba)
:::::

:::{note} Estructura de la consulta
:class: dropdown
:open:

La consulta utiliza varios términos con prefijo wikibase:xxx cuyo uso conviene aclarar.
+ (#1) Se pretende recorrer todas las propiedades aplicadas en Wikidata, para comprobar en cada una de ellas si se ha aplicado a wd:Q142. Todo ítem Pxxx que se declara como propiedad en Wikidata lo hace mediante una terna `<wd:Pxxx><rdf:type><wikibase:Property>`.
+ (#2) Los item propiedad wd:Pxxx recuperados no se usan así al aplicarlos como propiedad en una terna sino como wdt:Pxxx. Cada propiedad tiene asociada una terna que relaciona su identificador como ítem describible y su uso en enunciados directos, del tipo: `<wd:Pxxx><wikibase:directClaim><wdt:Pxxx>`. Cada ejecución en #2 encuentra este respectivo cambio de prefijo.
+ Mediante al acceso al servicio wikibase:label se obtienen todas las etiquetas principales, etiquetas alternativas y descripciones de cada variable en WHERE. (En el caso de los ítem propiedad wd:Pxxx, las etiquetas están asociados a éstos y no a identificadores wdt:Pxxx que tan sólo marcan su uso sintáctico con ese cambio de prefijo. Por eso en SELECT se pide `propLabel` en vez de ?propDirectaLabel).
:::

**Enunciados indirectos (todos)**. La relación de enunciados indirectos, con su información adicional, es la que se aprecia en la página html del ítem. La siguiente consulta devuelve estas asignaciones indirectas para el ítem wd:Q142, con un nodo-statement interpuesto. No se recoge la información añadida a estos nodos-statement. 

:::::{hint} Consulta: sobre Q142, todos sus enunciado indirectos
:label: query-todos-enunciados-indirectos-sobre-item
:class: dropdown
:open: 
```SPARQL
SELECT ?sujeto ?propHaciaStmt ?propLabel ?stmt ?propEnStmt ?valor
WHERE {
  BIND (wd:Q142 AS ?sujeto)                        # 0 Ejemplo aplicado a ?sujeto = wd:Q142
  ?prop rdf:type wikibase:Property .               # 1 Encuentra cada wd:Pxxx propiedad en Wikidata
  ?prop wikibase:claim ?propHaciaStmt .            # 2 De cada wd:Pxxx se obtiene su forma p:Pxxx
  ?prop wikibase:statementProperty ?propEnStmt .   # 3 De cada wd:Pxxx se obtiene su forma ps:Pxxx
  ?sujeto ?propHaciaStmt ?stmt .                   # 4 (sujeto, p:Pxxx, id_enunciado)
  ?stmt ?propEnStmt ?valor .                       # 5 (id_enunciado, ps:Pxxx, valor_indirectamente_asignado)
  SERVICE wikibase:label { bd:serviceParam wikibase:language "es,en". }
}
ORDER BY ?propHaciaStmt
```
[Ejecución en Wikidata](https://w.wiki/6f7q)
:::::

### Sección de identificadores externos
Existen otras mucha bases de datos RDF públicas, usualmente más especializadas que Wikidata. Todas ellas forman la estructura de la Web de Datos Abiertos Enlazados y se mantienen de forma independiente aunque respetando unos estándares comunes. Estos estándares, como RDF, permiten a Wikidata incluir entre las ternas propias que describen a Miguel de Cervantes (Q5682) la siguiente:\
`wd:Q5682 wdt:P950 https://datos.bne.es/resource/XX1718747`\
que se puede leer como:
+ _el mismo concepto que el de mi ítem Q5682_
+ _tiene como identificador en la Biblioteca Nacional de España_ (P950) 
+ _...XX1718747_ 

Es decir, se reconoce aquí que 'aquí y allí' se está describiendo el mismo concepto y se informa sobre cuál es el identificador asignado 'allí'. La propiedad P950 de Wikidata es la que se usa cada vez que se informa sobre el identificador de un concepto en la base de datos de la BNE. 

:::::{hint} Consulta: todas las referencias a ítems en la BNE
:label: query-todas-referencias-BNE
:class: dropdown
:open: 
La siguiente consulta recopila de forma transversal en Wikidata todos los ítems que se han relacionado con sus correspondientes en la BNE (porque todos ellos se han declarado mediante la propiedad P950: 'tiene por identificador en la BNE').

```SPARQL
SELECT ?idWikidata ?idWikidataEtiq ?idBNE ?urlBNE
WHERE {
  ?idWikidata wdt:P950 ?idBNE .
  ?idWikidata   wdtn:P950 ?urlBNE .
  ?idWikidata rdfs:label ?idWikidataEtiq. 
  FILTER(LANG(?idWikidataEtiq)="en")
}
```
[Ejecución en Wikidata](https://w.wiki/6f8D)
:::::

En la página html de cada entidad, bajo esta sección 'Identifiers', se enumeran los identificadores en los diversos repositorios de la Web de Datos donde se está describiendo el mismo concepto.

Wikidata reserva para cada repositorio externo una propiedad que lo referencia, como ocurre con P950 para todas las referencias a identificadores en la BNE. Hay casi 8000 repositorios externos referenciados; es decir, este tipo de propiedades que remiten a otro repositorio suman casi ocho mil, dentro de las casi once mil propiedades de Wikidata.

### Sección de enlaces a sitios internos
Enlaces a artículos de wikidata en sitios internos diversos. La siguiente consulta retorna todas las entradas sobre _Francia_ (Q142) en los diversos sitios de los proyectos de la Fundación WikiMedia.

:::::{hint} Consulta: sobre Q142, todos los enlaces dentro de WikiMedia
:label: query-todos-enlaces-internos-wikimedia
:class: dropdown
:open: 
```SPARQL
SELECT ?artículo ?idioma ?sitio
WHERE {
  ?artículo schema:about wd:Q142 ;
	        schema:inLanguage ?idioma ;
	        schema:isPartOf ?sitio .
}
ORDER BY ?idioma
```
[Ejecución en Wikidata](https://w.wiki/6f8N)
:::::


## Ficheros descargables sobre un item
La misma información facilitada en la página html de un ítem se puede obtener en diferentes formatos, finalizando esa dirección con la extensión del formato. Por ejemplo, las tripletas sobre _Etanol_ (Q153) se pueden recuperar en los siguientes formatos:
+ HTML: [http://www.wikidata.org/entity/Q153.html](http://www.wikidata.org/entity/Q153.html), o bien sin extensión .html
+ N-tripletas: [http://www.wikidata.org/entity/Q153.nt](http://www.wikidata.org/entity/Q153.nt)
+ Turtle: [http://www.wikidata.org/entity/Q153.ttl](http://www.wikidata.org/entity/Q153.ttl)
+ Json: [http://www.wikidata.org/entity/Q153.json](http://www.wikidata.org/entity/Q153.json)
+ RDF/XML: [http://www.wikidata.org/entity/Q153.rdf](http://www.wikidata.org/entity/Q153.rdf)

La versión Turtle es la más legible. Para refinar algunas consultas SPARQL conviene, a veces, echar un vistazo a esta presentación de un ítem.