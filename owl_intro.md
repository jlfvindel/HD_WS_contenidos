# Avance sobre ontologías OWL
Las conceptualizaciones RDFS se pueden detallar más, incorporando nuevas características para las clases y propiedades. OWL permite declarar este tipo de conceptualizaciones en un mismo lenguaje estándar.

## OWL, como ampliación de RDFS
El lenguaje de conceptualización RDFS no es suficientemente expresivo, por ejemplo: 
+ No permite declarar que dos clases son disjuntas, es decir, que no pueden tener instancias en común. 
+ Tampoco permite declarar que una determinada propiedad es simétrica, es decir, que si ocurre "X prop Y", debe ocurrir "Y prop X".

### Nuevos constructores en OWL
*Más expresivo que RDFS*. El lenguaje de conceptualización OWL 2 sí que facilita nuevas definiciones y restricciones sobre clases y propiedades. Pero, de nuevo, resulta a su vez limitado: no se pueden añadir todas las restricciones imaginables sobre clases o propiedades. 

*Límites teóricos estudiados*. Esto ocurre porque hay limitaciones teóricas inevitables: en algunos casos, la inferencia con ciertas restricciones resulta indecidible, es decir, sin garantías de que la computación finalice en todo caso. O bien puede resultar computacionalmente demasiado compleja: un ligero aumento en el número de datos procesados puede producir un aumento inasumible en el tiempo de finalización del cálculo.

Para acotar teóricamente la indecibilidad y la complejidad se ha trabajado cuidadosamente sobre la semántica de los constructores OWL añadidos, basados en fragmentos escogidos de la Lógica de Primer Orden (reformulados como Lógicas Descriptivas).


### Uso de OWL en la Web de Datos
*Vocabularios ligeros*. Los repositorios públicos RDF, en la Red de Datos Abiertos Enlazados (o Web de Datos), mayoritariamente superponen una conceptualización sencilla a sus datos, en forma de vocabularios RDFS.

*Ontologías complejas*. En el otro extremo, hay repositorios muy especializados (públicos o no) que requieren una conceptualización compleja superpuesta. Sería el caso de una taxonomía completa de los seres vivos, con su jerarquía compleja de clases y de propiedades. O de repositorios especializados, por ejemplo, en bioquímica. Para estos casos se utilizan ontologías OWL cuidadosamente diseñadas.

*RDFS-Plus*. Entre medias, hay un uso creciente de vocabularios RDFS con unas pocas funcionalidades OWL escogidas. RDFS-Plus es una selección concreta de funcionalidades OWL, como extensión de RDFS, que se usa para este nivel de expresividad intermedia.


## El vocabulario OWL 2
Como ocurre con RDFS, el lenguaje OWL 2 permite declarar vocabularios u ontologías propias con declaraciones estándar sobre las características y restricciones de sus individuos, clases y propiedades.


### Individuos idénticos o distintos
*Identidad y diferenciación*. En OWL se puede declarar que dos instancias, dos identificadores para individuos, son el mismo; es decir, deben siempre interpretarse como referencias al mismo objeto externo. O, por el contrario, también se puede declarar que varios identificadores de individuos son todos ellos entre sí distintos: debe interpretarse cada uno como referencia a un objeto distinto.

*Clases enumeradas*. Con varios elementos distintos se puede además declarar una clase C, compuesta exactamente por esos elementos: $C = \{e_1,...,e_k\}$.

### Propiedades y sus características
En RDFS, los objetos podían ser identificadores o literales. Esto mismo divide a las propiedades OWL entre Object Properties (con objeto recurso) o Datatype Property (con objeto literal).

*Características de las propiedades*. Dentro de las Object Properties se puede fijar que la propiedad es de cualquiera de estos tipos: transitiva, simétrica, asimétrica, reflexiva, irreflexiva, funcional o con inversa funcional. Por ejemplo, visualizado como un grafo, una relación funcional garantiza que de un nodo sale a lo sumo un enlace a un nodo: no puede ocurrir que salgan dos o más enlaces a nodos distintos. Si la relación es de tipo inversa funcional lo que no puede ocurrir es que a un nodo lleguen dos o más enlaces desde nodos distintos.

*Cotas universales*. Para acotar la jerarquía de clases y subclases, OWL facilita una clase superior (que contiene todo recurso) y otra inferior (que no contiene ninguno). Adicionalmente, para acotar la jerarquía de subpropiedades se define una propiedad superior, aquella que relaciona cada par de individuos. Y, dualmente, también se define la propiedad inferior, aquella que no relaciona ningún individuo con otro.

*Relaciones y composiciones entre propiedades*. También puede definirse una propiedad como inversa de otra. O declarar que dos propiedades son equivalentes, y por tanto relacionan exactamente los mismos pares de individuos. O, por el contrario, declarar que dos propiedades son disjuntas y no hay ningún par de recursos relacionados por ambas. Asimismo se puede definir una propiedad por composición de otras, por ejemplo, la de ser 'tío de' por composición de la relaciones 'ser hermano de' y 'ser padre de'.


### Construcción de clases complejas
Una ontología OWL puede tener sus clases primitivas, que son identificadores directamente declarados clases. Y adicionalmente se pueden definir nuevas clases mediante dos procesos entrelazables: (1) mediante operaciones sobre clases previas o bien (2) como conjunto de elementos que cumplen cierta restricción, expresada mediante propiedades y clases previas.

#### Definición booleana de nuevas clases
Se pueden definir nuevas clases como intersección o unión de dos clases previas o como complementaria de una clase. Así, por ejemplo, se puede definir la clase resultante de "los vertebrados y no marinos, unión con los anélidos". Y a partir de esta clase se puede definir su complementaria o su unión o intersección con otra, primitiva o compleja.

#### Definición a partir de propiedades y de otras clases
De un elemento, en la representación gráfica de una ontología, pueden o no salir enlaces de una cierta propiedad. Esto permite:
1. Definir nuevas clases, que agrupan elementos con una cardinalidad similar de estos enlaces (sin importar dónde acaban)
2. O refinar estas definiciones exigiendo que además acaben en algún elemento determinado
3. O que además acaben en elementos de una clase determinada

*(n P)*. En el primer caso, a partir de la propiedad _P_ 'habla_idioma' se podría definir la clase de los 'políglotas': elementos de los que salen _n_ enlaces (dos o más) de esa propiedad. O la de los 'bilingües': elementos de los que salen exactamente dos enlaces (_n_=2) de esa propiedad.

*(P a)*. En el segundo caso, si se exige que el enlace de tipo _P_ finalice en un determinado elemento _a_ se puede definir la clase de los 'francófonos': aquéllos cuyo enlace 'habla_idioma' finaliza en el individuo 'francés'. O, a partir de la propiedad 'tener_confianza_en' definir los la clase de los 'seguros': aquéllos de los que sale un enlace de esa propiedad que acaba en ellos mismos.

*(n P C)*. De forma parecida a la definición de los 'políglotas', con dos o más enlaces 'habla_idioma' (sin importar dónde acaban), se puede definir una clase como 'políglota indoeuropeo' si se requiere que esos enlaces finalicen en algún idioma de la clase C (idiomas indoeuropeos).

*(P C)* Un 'políglota indoeuropeo', además de hablar dos o más idiomas indoeuropeos, puede quizá hablar idiomas de otra clase. Se puede definir otro tipo de restricción, sin considerar ahora cuántos enlaces salen de un elemento, pero sí exigiendo que todos ellos acaben en la clase C de los idiomas indoeuropeos. Este conjunto de elementos lo serían de una clase que puede llamarse 'hablante a lo sumo indoeuropeo': que habla 0, 1, 2 o más idiomas pero todos ellos indoeuropeos. Esa restricción 'todos ellos acaben en C' (universal) se puede sustituir por 'al menos uno de ellos acabe en C' (existencial) y se podría definir así la clase de los 'hablantes como mínimo indoeuropeo', porque habla uno o más idiomas indoeuropeos (y quizá otros, en otras clases).


## Referencias y herramientas
### Referencias
La introducción oficial a OWL 2 se encuentra en:
+ Hitzler, P., Krötzsch, M., Parsia, B., Patel-Schneider, P. F., & Rudolph, S. (2009). [OWL 2 web ontology language primer](https://www.w3.org/TR/owl2-primer/). W3C recommendation, 27(1), 123.

Un texto público sobre ingeniería ontológica se encuentra disponible en la dirección:
+ Keet, C. M. (2018). [An introduction to ontology engineering](https://people.cs.uct.ac.za/~mkeet/OEbook/). University of Cape Town

El siguiente texto, impreso y no público, contiene una introducción completa y muy comprensible sobre OWL 2:
+ Hogan, A. (2020). _Web of data_. Springer International Publishing. \[Capítulo 5\]
+ Si no se puede acceder al texto anterior, el autor facilita diapositivas propias de sus cursos en la página de apoyo al texto, en [https://aidanhogan.com/webofdatabook/](https://aidanhogan.com/webofdatabook/).

Un libro completo y con una perspectiva aplicada:
+ Hendler J., Gandon F., Allemang D. (2020) _Semantic Web for the Working Ontologist: Effective Modeling for Linked Data, RDFS, and OWL_ (Third Edition) ACM Books.

### Editores especializados
Para el diseño de ontologías complejas es preferible disponer de un buen editor especializado. **Protégé** es el más utilizado para estos fines. Permite gráficamente añadir y gestionar individuos, clases y propiedades. Consta de una gran variedad de módulos activables para la edición, visualización y depuración de ontologías, entre los que se encuentran varios razonadores.
+ Se puede descargar o ejecutar como servicio en Protégé: [https://protege.stanford.edu/](https://protege.stanford.edu)
+ Entre otra documentación, hay dos tutoriales de diseño de ontologías con Protégé: [Pizza Tutorial](https://drive.google.com/file/d/1A3Y8T6nIfXQ_UQOpCAr_HFSCwpTqELeP/view) y [People Example Ontology](https://drive.google.com/file/d/1QgDoHuFwgLOj2_EhpJTpQA9vTAhF7-IP/view?usp=sharing)

### Paquetes Python
La gestión de tripletas RDF se puede realizar completamente mediante paquetes Python como [rdflib](https://rdflib.readthedocs.io/en/stable/). Para el uso, más avanzado, de ontologías OWL se puede utilizar el paquete [Owlready2](https://owlready2.readthedocs.io/en/latest/), que permite no sólo definir y gestionar la ontología sino insertarla en una aplicación que la explote para alguna finalidad determinada.

Un libro dedicado a la creación de ontologías con OWLReady2 es:
+ Lamy Jean-Baptiste (2020) _Ontologies with Python: Programming OWL 2.0 Ontologies with Python and Owlready2_. Apress

### Bases de datos especializadas
Las bases de datos orientadas a grafos se pueden clasificar conforme al modelo de grafo que gestionan. Entre las que administran tripletas RDF se encuentran por ejemplo [GraphDB](https://graphdb.ontotext.com), que puede incorporar [ontologías en GraphDB](https://graphdb.ontotext.com/documentation/10.0/devhub/ontologies.html). Gestores de otros modelos de grafos, como [Neo4J](https://neo4j.com/download/) permiten [incorporar ontologías](https://neo4j.com/blog/ontologies-in-neo4j-semantics-and-knowledge-graphs/), en este caso a través del módulo [Neosemantics](https://neo4j.com/labs/neosemantics/4.0/importing-ontologies/).

La explotación desde una aplicación externa de estas ontologías, albergadas en bases de datos, requiere APIs de acceso. [GraphDB](https://graphdb.ontotext.com/documentation/10.1/clients-and-apis.html) facilita varias APIs de acceso, así como [Neo4J](https://neo4j.com/docs/).