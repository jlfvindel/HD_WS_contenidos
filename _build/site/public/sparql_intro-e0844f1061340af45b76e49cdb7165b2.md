# Consultas SELECT (NobelPrize)

[SPARQL 1.1](https://www.w3.org/TR/2013/REC-sparql11-overview-20130321/) es un conjunto de estándares con tres funcionalidades  complementarias:
+ Es un protocolo de comunicación con servidores de datos RDF
+ Es un lenguaje de consulta de esos datasets RDF, que devuelve datos como respuesta
+ Es un lenguaje de administración de esos datos, con opciones de borrado, inserción y actualización

:::{important} Sobre esta sección
Se comienza el estudio de SPARQL como lenguaje de consulta. En esta modalidad se pueden remitir dos tipos de consultas a un dataset: de tipo SELECT (que devuelve relaciones entre N entidades en forma de tabla de N columnas) y de tipo CONSTRUCT (que devuelve grafos, conjuntos de tripletas).

+ **Esta sección se dedica al estudio introductorio de las consultas SELECT**
+ Como dataset de consulta se ha escogido el que mantiene la Fundación Nobel
+ La ejecución de estas consultas se puede realizar sobre el interfaz de consulta facilitado por la Fundación Nobel
:::

El primer apartado, *Consultas exploratorias*, facilita las consultas generales que se pueden realizar a un dataset RDF del que no se conoce todavía su estructura (sus clases, sus propiedades, etc). Se presenta la estructura general de una consulta SELECT y ejemplos con instrucciones para ejecutarlos sobre el interfaz de consulta oficial de la Fundación Nobel.

El segundo apartado, *El vocabulario NobelPrize*, describe la estructura conceptual que alberga los datos de ese datasets: los nombres de sus clases y propiedades. Y facilita ejemplos de consulta que ya utilizan esa información específica en sus patrones de búsqueda.

Los siguientes apartados de esta sección resumen, con ejemplos ejecutables, los patrones de búsqueda básicos en consultas SPARQL de tipo SELECT.

