# Consultas SELECT (Wikidata)

:::{attention} Avance
Esta sección tiene dos objetivos complementarios:
+ Por un lado, se continúa trabajando sobre consultas SPARQL de tipo SELECT
+ Por otro lado, las consultas ahora se ejecutan sobre el dataset de Wikidata para facilitar el estudio de su estructura.
:::

## Sobre Wikidata
Wikidata ([https://www.wikidata.org](https://www.wikidata.org)) es una base de conocimiento generalista formada por enunciados que describen, clasifican y relacionan millones de ítems (entidades físicas o conceptos). Por su extensión, por su tamaño y por su construcción comunitaria  es un dataset especialmente importante entre los que aparecen en la Web Semántica. 

Wikidata ha desarrollado un modelo de datos complejo porque es un repositorio coeditado, generalista y que pretende recoger *lo que otros afirman* con registro de *fuentes* y de otros datos complementarios. Estas particularidades se aprecian enseguida por el uso de propiedades como P31 y P279 respectivamente para reemplazar las asignaciones a clases y la declaración subclases. Y también por el uso de enunciados indirectos y su duplicación como enunciados directos.

El uso básico de Wikidata, una vez familiarizados con las peculiaridades más repetidas, permite la recuperación de cientos de miles de datos sobre categorías diversas y muy interrelacionadas.

## Consultas SELECT en Wikidata
Tras las consultas presentadas en esta sección se puede continuar con las del propio [Tutorial sobre SPARQL de Wikidata](https://www.wikidata.org/wiki/Wikidata:SPARQL_tutorial). Este recursos y otros más avanzados se pueden encontrar en el [Portal de Ayuda de Wikidata](https://www.wikidata.org/wiki/Help:Contents), más bien enfocado a facilitar el trabajo de los coeditores de datos.




