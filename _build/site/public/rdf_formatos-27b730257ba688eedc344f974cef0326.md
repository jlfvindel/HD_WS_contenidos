# Cómo transmitir grafos RDF

El siguiente diagrama visualiza las relaciones entre datos en un grafo RDF. En este caso, para acortar los identificadores se asume que el prefijo `u:` es una abreviatura de `http://datos.uned.es/ej/`

:::::{hint} Ejemplo de grafo RDFE
:label: ej-grafo2
:::mermaid
flowchart TD
    Q(u:Quijote) -->|u:autor| C(u:Cervantes)
    N(u:Novelas_ejemplares) -->|u:autor| C
    C -->|u:lugar_nacimiento| A(u:Alcalá_de_Henares)
    C -->|u:fecha_nacimiento| F[["29/09/1547"]]
    A -->|u:comparte_frontera| M(u:Meco)
    A -->|u:comparte_frontera| T(u:Torrejón de Ardoz)
:::
:::::

Hay varias sintaxis posibles para describir este grafo textualmente, de forma secuencial. En esta sección tan sólo se introduce la familia de sintaxis Turtle:
+ **N-Triples** (y N-Quads): la más adecuada para la transmisión explícita de enunciados entre máquinas (y por tanto, la menos legible por personas)
+ **Turtle** (y TriG): la más directamente comprensible por personas

Otras dos sintaxis utilizadas, algo menos legibles que Turtle, son **JSON-LD** y **RDF/XML**. Todas ellas aparecen brevemente descritas en la introducción oficial a RDF del consorcio W3: [RDF 1.1 Primer](https://www.w3.org/TR/rdf11-primer/#section-graph-syntax).

## Dos formatos RDF sugeridos
N-Triples es el formato más explícito de secuenciación de datos RDF, muy adecuado para la transmisión de datos entre máquinas pero muy poco legible para una persona.

Turtle es una derivación de este formato con facilidades sintácticas añadidas que producen una secuenciación más concisa y mucho más legible y comprensible.

### N-Triples
El [ejemplo de grafo RDF inicial](#ej-grafo2) se puede secuenciar como sigue.

:::::{hint} Enunciados RDF en formato N-Triples
:label: ej-grafo2-ntriples
```n-triples
<http://datos.uned.es/ej/Quijote> <http://datos.uned.es/ej/tiene_autor> <http://datos.uned.es/ej/Cervantes> .
<http://datos.uned.es/ej/Novelas_ejemplares> <http://datos.uned.es/ej/tiene_autor> <http://datos.uned.es/ej/Cervantes> .
<http://datos.uned.es/ej/Cervantes> <http://datos.uned.es/ej/lugar_nacimiento> <http://datos.uned.es/ej/Alcala_de_Henares> .
<http://datos.uned.es/ej/Cervantes> <http://datos.uned.es/ej/fecha_nacimiento> "29/09/1547" .
<http://datos.uned.es/ej/Alcala_de_Henares> <http://datos.uned.es/ej/comparte_frontera> <http://datos.uned.es/ej/Meco> .
<http://datos.uned.es/ej/Alcala_de_Henares> <http://datos.uned.es/ej/comparte_frontera> <http://datos.uned.es/ej/Torrejon_de_Ardoz> .
```
:::::

Cada enunciado se expresa en una línea, finalizada por un punto. Los términos del enunciado que son identificadores muestran el identificador por completo y encerrado entre corchetes angulares. Los términos que son literales se muestran encerrados entre comillas. Los nodos en blanco se expresan como, por ejemplo, el término \_:xb (con un nombre cualquiera interno como es xb).

:::{hint} Actividad propuesta
:label: activ-rdfshape-ntriples-sintaxis
Para comprobar la correcta sintaxis de los enunciados anteriores se sugiere utilizar el servicio RDFShape (en https://rdfshape.weso.es):
+ Se pueden copiar los enunciados anteriores y pegarlos en RDFShape en su pestaña [RDF :: Analysis and Visualization](https://rdfshape.weso.es/dataInfo)
+ Se debe especificar que el formato de los enunciados en N-Triples. Como inferencia se debe escoger *None*.

El sistema analiza sintácticamente el texto pegado, detecta que consta de 6 enunciados y facilita la visualización de sus datos en dos tipos de presentación distintos.
:::

### Turtle
La [secuenciación anterior](#ej-grafo2-ntriples), en formato N-Triples, se puede expresar **de forma mucho más concisa y legible** utilizando las convenciones del formato Turtle:

:::::{hint} Enunciados RDF en formato Turtle
:label: ej-grafo2-turtle
```turtle
PREFIX u: <http://datos.uned.es/ej/>

u:Quijote u:autor u:Cervantes .
u:Novelas_ejemplares u:autor u:Cervantes .
u:Cervantes u:lugar_nacimiento u:Alcala_de_Henares ;
            u:fecha_nacimiento "29/09/1547" .
u:Alcala_de_Henares u:comparte_frontera u:Meco, u:Torrejon_de_Ardoz .
```
:::::

Los términos que son identificadores resultan ahora más legibles porque se abrevia la parte inicial del identificador como `u:`, que se declara inicialmente sustituto del prefijo `<http://datos.uned.es/ej/>`.

El primer enunciado con *Cervantes* como sujeto acaba en punto y coma. El punto y coma indica que comienza otro nuevo enunciado que mantiene implícitamente el sujeto del anterior (*Cervantes*).

El primer enunciado con *Alcala_de_Henares* como sujeto acaba en coma. La coma indica que comienza otro nuevo enunciado que mantiene implícitos tanto el sujeto como la propiedad del anterior (*Cervantes comparte_frontera*).

:::{hint} Actividad propuesta
:label: activ-rdfshape-turtle-conversion
RDFShape facilita un conversor entre formatos RDF. Para familiarizarse con su uso se propone:
+ Copiar los enunciado Turtle anteriores y pegarlos en su pestaña [RDF :: Conversion](https://rdfshape.weso.es/dataConvert)
+ Especificar como formato de entrada Turtle y como formato de salida N-Triples (con inferencia *None*). Se puede comprobar si esta salida coincide con la secuenciación N-Triples facilitada anteriormente en este texto para el mismo grafo.
:::

## Otras opciones de secuenciación
Como ampliación de actividad anterior, se puede solicitar en RDFShape la conversión a otros formatos del mismo grafo RDF. 
+ Los formatos RDF se presentan brevemente en [RDF 1.1 Primer (5. Writing RDF graphs)](https://www.w3.org/TR/rdf11-primer/#section-graph-syntax).
+ Una exposición más detallada se encuentra en [Web of Data, secc. 3.8](https://aidanhogan.com/wodata/book.pdf#page=110) (2020, Aidan Hogan).
