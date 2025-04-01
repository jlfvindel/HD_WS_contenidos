# Grafos de conocimiento

## Contexto
La página de Wikipedia sobre [Artificial Intelligence](https://en.wikipedia.org/wiki/Artificial_intelligence)) incluye a la derecha un menú jerarquizado de temas. Si se despliega la sección *Approaches* se muestran varias líneas históricas de diseño de sistemas inteligentes, que se están considerando conjuntamente en el diseño de sistemas híbridos. Resumidamente, se contemplaban dos grandes estrategias de diseño que se presentan en el siguiente desplegable.


:::{note} Sistemas entrenables y sistemas basados en conocimiento
:class:dropdown

Sistemas conexionistas entrenables
: Este el caso de los sistemas que aprenden mediante [Machine Learning](https://en.wikipedia.org/wiki/Machine_learning) y en particular mediante [Deep Learning](https://en.wikipedia.org/wiki/Deep_learning). Dentro de estos paradigmas han resultado de gran efectividad los LLM o [Large Language Model](https://en.wikipedia.org/wiki/Large_language_model) que se han popularizado en forma de servicios como ChatGPT u otros similares.

Sistemas basados en conocimiento explícito y legible 
: Esta aproximación se basa en la expresión simbólica de conocimiento de forma que podía ser leído, enlazado y modificado por personas. Y sobre el que se podían programar sistemas inteligentes que consultaban y explotaban este conocimiento. Esta estrategia se encuentra descrita en la entrada [Symbolic](https://en.wikipedia.org/wiki/Symbolic_artificial_intelligence) y en otras subsecciones como [Knowledge Representation and Reasoning](https://en.wikipedia.org/wiki/Knowledge_representation_and_reasoning).
: La representación de conocimiento que se usa más intensivamente en estos últimos años se articula en forma de [Knowledge Graphs](https://en.wikipedia.org/wiki/Knowledge_graph). Hay diversas propuestas de modelos de grafos sobre los que se pueden recoger relaciones entre conceptos y su categorización.
: Muy particularmente, se planteó una iniciativa para propiciar la publicación opcional de estos grafos de conocimiento en la Web por parte de distintas instituciones de forma que cualquiera pueda declarar conexiones explícitas entre ellos. De esta forma, y como se hizo con HTML y la Web usual de documentos, se pretende la construcción  social y descentralizada y de una Web de [Datos Enlazados](https://en.wikipedia.org/wiki/Linked_data) o [Web Semántica](https://en.wikipedia.org/wiki/Semantic_Web).
: La publicación de grafos accesibles y enlazables acotaba algunos requisitos sobre cómo debían ser los identificadores de los conceptos enlazados y los modelos de grafos permitidos sobre esta infraestructura Web. El consorcio W3 ha desarrollado todos los estándares necesarios para ello, como el uso común de un tipo de grafo ([RDF](https://en.wikipedia.org/wiki/Resource_Description_Framework)), de lenguajes de conceptualización para superponer taxonomías y ontologías ([RDFS](https://en.wikipedia.org/wiki/RDF_Schema) y [OWL](https://en.wikipedia.org/wiki/Web_Ontology_Language)) o de un lenguaje de consulta y administración de datos como [SPARQL](https://en.wikipedia.org/wiki/SPARQL).
:::


:::{important} Sobre el ámbito de este documento
Las secciones de este documento se refieren a los estándares y tecnologías de gestión de grafos de conocimiento tal y como se requieren en la Web Semántica: RDF(S), SPARQL y OWL. 
:::

Estas mismas tecnologías se pueden utilizar en el diseño de sistemas inteligentes locales o propios, aunque para estos usos compiten con otros modelos de grafos como p. ej. los [Property Graphs](https://en.wikipedia.org/wiki/Property_graph) y otros lenguajes de consulta como p. ej. [Cypher](https://en.wikipedia.org/wiki/Cypher_(query_language)).


## Sobre este documento
Este documento es una introducción al modelado y a la consulta de grafos RDF. Consta de las siguientes partes:
1. Introducción a RDF
2. Introducción a SPARQL
    + Consultas de tipo SELECT (sobre NobelPrize)
    + Consultas de tipo SELECT (sobre Wikidata)
3. Anexo: breve introducción a OWL

## Bibliografía comentada

### Libros generalistas y de contexto

:::{admonition} Linked Data for Digital Humanities
:class: dropdown
[Linked Data for Digital Humanities](https://www.taylorfrancis.com/books/mono/10.4324/9781003197898/linked-data-digital-humanities-terhi-nurmikko-fuller), *Nurmikko Fuller, Terhi*; 2023, Routledge.

Libro adecuado para una primera aproximación de contexto sobre las tecnologías de la Web Semántica y su utilidad, especialmente para los investigadores en Humanidades Digitales.
:::

:::{admonition} The Accidental Taxonomist (3rd edition)
:class: dropdown
[The Accidental Taxonomist](https://www.hedden-information.com/accidental-taxonomist/), *Hedden, Heather*; 2022, Medford, Information Today Inc. ([Web del libro](https://www.hedden-information.com/accidental-taxonomist/websites/)) 

Recomendable para los interesados en la modelización de metadatos en forma de taxonomías.
:::

### Libros técnicos sobre RDF(S), OWL y consultas SPARQL

:::{admonition} Semantic Web for the Working Ontologist
:class: dropdown
[Semantic Web for the Working Ontologist](https://dl.acm.org/doi/book/10.1145/3382097), *Allemang, Dean; Hendler, Jim; Gandon, Fabien;* 2020, ACM Books

Presenta de forma comprensible las opciones de uso de las tecnologías de la Web de Datos desde una perspectiva aplicada.
:::

:::{admonition} Learning Sparql
:class: dropdown
[Learning Sparql (2nd edition)](https://www.oreilly.com/library/view/learning-sparql-2nd/9781449371449/), *DuCharme, Bob*; 2013, O'Really Media.

Facilita una introducción detallada al lenguaje de consulta SPARQL 1.1. El autor mantiene una ([Web del libro](https://www.learningsparql.com)), con recursos y referencias complementarias a su [blog](https://www.bobdc.com/blog/).
:::

:::{admonition} Web of Data
:class: dropdown
[Web of Data](https://link.springer.com/book/10.1007/978-3-030-51580-5) *Hogan, Aidan*; 2020, Springer. 

Es un texto extenso declarativo, informativo, sobre los estándares de la Web Semántica, que describe con muchos ejemplos intercalados. Se puede usar como referencia comentada de estos estándares. En [la página del autor sobre el libro] https://aidanhogan.com/webofdatabook/ se facilitan recursos adicionales, entre los que se encuentra [una versión pre-print](https://aidanhogan.com/wodata/book.pdf) del libro.
:::


### Referencias técnicas oficiales del W3
:::{admonition} RDF(S) 1.1
:class: dropdown
[RDF 1.1 Primer](https://www.w3.org/TR/rdf11-primer/): introducción a RDF que remite a otros documentos ampliatorios.
:::

:::{admonition} SPARQL 1.1
:class: dropdown
[SPARQL 1.1 Overview](https://www.w3.org/TR/sparql11-overview/): presentación del lenguaje que remite a su uso como lenguaje de consulta (descrito en [SPARQL 1.1 Query Language](https://www.w3.org/TR/2013/REC-sparql11-query-20130321)) y a su uso como lenguaje de administración de una base de datos RDF (descrito en [SPARQL 1.1 Update](https://www.w3.org/TR/2013/REC-sparql11-update-20130321)).
:::

:::{admonition} OWL 2
:class: dropdown
[OWL 2 Primer](https://www.w3.org/TR/owl2-primer/): introducción al lenguaje OWL para la declaración de ontologías complejas.
:::

:::{admonition} Sobre las nuevas versiones RDF 1.2 y SPARQL 1.2
:class: dropdown
[RDF 1.2 Primer](https://w3c.github.io/rdf-primer/spec/) es una introducción a la nueva especificación RDF del W3, aún en proceso de aprobación. Esta ampliación de RDF permitirá referenciar en un enunciado no sólo a un objeto \<o\> (como en \<s\>\<p\>\<o\>) sino a toda una tripleta como objeto (como en \<s\> \<p\> \<\<S P O\>\>).

Esta ampliación sintáctica y semántica obliga a redefinir el resto de los estándares asociados, como son los diversos formatos de transmisión de tripletas RDF o el lenguaje de consulta.
:::

### Servicios, herramientas y programación sobre grafos RDF

:::{admonition} Playgrounds en línea para RDF
:class: dropdown
Hay servicios en línea amigables para analizar, visualizar o cambiar de formato conjuntos de tripletas RDF. Además permiten comprobar que cumplen una estructura declarada por el autor y también se facilita la ejecución de consultas SPARQL sobre estos datasets. Entre estos espacios de pruebas se encuentran:
+ [RDFShape](https://rdfshape.weso.es) facilitado por el grupo de investigación WESO de la Universidad de Oviedo.
+ [RDF Playground](https://rdfplayground.dcc.uchile.cl) facilitado por la Universidad de Chile.
:::

:::{admonition} Gestión de ontologías OWL (en línea y localmente)
:class: dropdown
[Protégé](https://protege.stanford.edu), entre otras opciones similares, es el editor de ontologías OWL más utilizado. Permite además todo tipo de acciones de análisis, depuración mediante razonadores y consulta sobre estas ontologías. Se facilita (gratuitamente) como servicio en línea () o como aplicación local.

Como introducción a su uso se puede utilizar la versión del [Pizza Tutorial for Protége](https://www.michaeldebellis.com/post/new-protege-pizza-tutorial) de Michael DeBellis, así como otras entradas de su [blog](https://www.michaeldebellis.com/blog).
:::

:::{admonition} Bases de datos para grafos RDF (y para otros modelos de grafos)
:class: dropdown
**Grafos RDF**. Entre otras opciones, para la gestión de datasets RDF se puede utilizar [GraphDB](https://graphdb.ontotext.com)
+ [GraphDB Desktop](https://www.ontotext.com/products/graphdb) es una versión gratuita descargable, de instalación inmediata.

**Property Graphs**. Entre las bases de datos que usan internamente este modelo se encuentra [Neo4J](https://neo4j.com), también instalable localmente de forma gratuita.
+ [Neo4J Desktop](https://neo4j.com/download/) es la versión descargable, también de instalación inmediata como aplicación de escritorio.
+ Como ocurre con GraphDB, Neo4J facilita una gran diversidad de recursos de aprendizaje y de casos de uso. Entre ellos se encuentran [textos descargables](https://neo4j.com/books/) gratuitamente, como *Building Knowledge Graphs: A Practitioner's Guide*.

Una revisión extensa (y muy técnica) sobre los Grafos de Conocimiento en general se puede encontrar en Knowledge Graphs (Aidan Hogan et al, 2021) que se facilita en abierto en la dirección https://kgbook.org
:::

:::{admonition} Gestión y consulta de grafos RDF (y de ontologías OWL) mediante Python
:class: dropdown
+ [rdflib](https://rdflib.readthedocs.io/en/stable/) es la librería más utilizada para la gestión RDF. Permite cargar enunciados RDF en estructuras Python y consultarlos y administrarlos mediante órdenes SPARQL estándar.
+ [sparqlwrapper](https://sparqlwrapper.readthedocs.io/en/latest/) se utiliza para enviar consultas a datasets externos (usualmente en la Web Semántica) y para recoger estructuradamente los resultados emitidos desde estos servidores.
+ [owlready2](https://owlready2.readthedocs.io/en/v0.47/) permite cargar y gestionar ontologías complejas en estructuras Python para su depuración y consulta con ayuda de razonadores. El autor de este paquete escribió un texto sobre este tipo de gestión: [Ontologies with Python](https://link.springer.com/book/10.1007/978-1-4842-6552-9). *Lamy, Jean-Baptiste*; 2021, Apress.
:::
