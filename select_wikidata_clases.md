# Clasificaciones en Wikidata
Algunos ítems de Wikidata se usan como agrupadores de otros ítems, es decir, como clases. Hay clases, como la de los _Vertebrados_, que forman parte de la jerarquía conceptual de análisis de un área de conocimiento. Y hay otras clases puramente instrumentales en Wikidata, que se crean para agrupar ítems con una característica de interés para la base de datos del repositorio. Cada ítem se puede asignar a una o más clases. 

## Propiedades para clasificar
*Instancia y subclase*. Corresponden a las relaciones de pertenencia de un elemento a un conjunto y a la de subconjunto de elementos incluidos en otro conjunto.

*Parte y faceta*. Estas dos relaciones estrictamente no participan en la clasificación de entidades. La primera sirve para descomponer algo en componentes y la segunda para sugerir una relación difusa entre conceptos.


### Pertenencia
La clasificación de ítems se produce a través de la relación de pertenencia, que en RDF se formaliza como `rdf:type`. **En Wikidata se ha optado por una versión propia de esta relación (rdf:type) y para ello se usa la propiedad `P31` alternativamente**.

La siguiente consulta cuenta el número de instancias asignadas explícitamente en Wikidata a la clase _"ser\_humano"_ (Q5):

:::::{hint} Consulta: recuento de las instancias de la clase *'ser humano'*
:label: query-todas-instancias-de-una-clase
:class: dropdown
:open: 

```SPARQL
SELECT (COUNT(?s) AS ?sNum)
WHERE {
  ?s wdt:P31 wd:Q5.
}
```
[Ejecución en Wikidata](https://w.wiki/6VPb)
:::::

### Subclases

La relación estándar en RDF es `rdfs:subClassOf`. De nuevo, **Wikidata utiliza internamente una versión propia (de rdfs:subClassOf) en forma de la propiedad `P279`**. La siguiente consulta devuelve las trescientas clases declaradas directa y explícitamente como subclases de Q5.

:::::{hint} Consulta: todas las subclases directas de la clase *'ser humano'*
:label: query-todas-subclases-directas
:class: dropdown
:open: 
```SPARQL
SELECT ?clase ?claseLabel
WHERE {
  ?clase wdt:P279 wd:Q5 .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
```
[Ejecución en Wikidata](https://w.wiki/6Vm3)
:::::

**Jerarquía de subclases**. Las subclases de Q5 pueden tener a su vez sus propias subclases. Y éstas, a su vez, otras subclases. La siguiente consulta devuelve todas en la jerarquía de (sub)subclases de Q5. En total, unas 4.700 clases.

:::::{hint} Consulta: recursivamente, todas las subclases (directas y a más bajo nivel)
:label: query-todas-subclases-recursivamente
:class: dropdown
:open: 
```SPARQL
SELECT ?clase ?claseLabel
WHERE {
  ?clase wdt:P279+ wd:Q5 .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
```
[Ejecución en Wikidata](https://w.wiki/6VmB)
:::::

En esta consulta se ha utilizado el patrón de consulta `"?clase wdt:P279+ wd:Q5"`, donde el signo '+' significa: "una o más composiciones reiteradas del camino wdt:P279". Es decir, lo cumplen las entidades que sean subclases directas de Q5, pero también las que sean subclases de subclases de Q5 (`wdt:P279/wdt:P279`), las que estén a distancia 3 (`wdt:P279/wdt:279/wdt:P279`), etc.

```mermaid
flowchart LR
    SSSC[subsubsubclase] --> |wdt:P279| SSC[subsubclase]
    SSC --> |wdt:P279| SC[subclase]
    SC --> |wdt:P279| C[clase]
```

:::{important} Caminos de propiedades *(Property Paths)*
La composición de caminos indicada en wdt:P279+ es una de las opciones posibles en las búsquedas SPARQL. Se ejemplifican con P31 y P279 pero son aplicables a cualquier propiedad.
+ wdtP279+: una o más composiciones de esta propiedad;  wdtP279*: cero o más composiciones;  wdtP279?: cero o una composición.
+ wdt:P31/wdtP279*: un enlace de tipo pertenencia (P31) seguido de cero o más de tipo subclase (P279)
+ wdt:P31|wdt:P279: salto desde un nodo siguiendo o bien un enlace P31 o bien un enlace P279 
:::

Una consulta previa [contaba las instancias directas](#query-todas-instancias-de-una-clase) de la clase 'ser humano'. Otra consulta [contaba recursivamente todas las subclases](#query-todas-subclases-recursivamente) de la clase 'ser humano'. La siguiente consulta cuenta todas las instancias de alguna subclase de Q5, en cualquier nivel de inclusión:

:::::{hint} Consulta: recursivamente, todas las subclases (directas y a más bajo nivel)
:label: query-todas-instancias-de-todas-subclases
:class: dropdown
:open: 
```SPARQL
SELECT (COUNT(?s) AS ?sNum)
WHERE {
  ?s wdt:P31/wdt:P279* wd:Q5.
}
```
[Ejecución en Wikidata](https://w.wiki/6fDv)
Esta consulta considera: 
+ (`<s><wdt:P31><wd:Q5`) los elementos directos de Q5
+ (`<s><wdt:P31><X`, `<X><wdt:P279><wd:Q5>`) los elementos de cualquier subclase directa
+ (`<s><wdt:P31><X`, `<X><wdt:P279><Y>``<Y><wdt:P279><wd:Q5>`)  los de cualquier subsubclase
+ ... y cualquier elemento de una subclase (a cualquier nivel de anidamiento) de Q5
:::::

### Parte
La clase 'vehículo de motor' puede tener miles de instancias, todas ellas similares en tanto que cumplen por igual lo que se requiere para pertenecer a esta clase. Y esta clase puede tener subclases como 'camión', con sus instancias (que además lo son de la superclase que contiene a 'camión').
    
Ahora bien, el concepto de qué es un vehículo de motor, qué se precisa para ser reconocido como instancia de esa clase, requiere describir algunos componentes (o partes) y cómo interaccionan. Todas estas instancias se componen de partes como 'motor' o como 'rueda' (si no se concibe un vehículo de motor sin ruedas).
    
La siguiente consulta recupera enunciados de Wikidata en que se afirma que el sujeto es 'parte de' (P361) algo.
                   
:::::{hint} Consulta: enunciados que declaran 'parte de' algo
:label: query-todas-parte-de
:class: dropdown
:open:                                                                                                   
```SPARQL
SELECT ?parte ?parteLabel ?parte_de ?todo ?todoLabel
WHERE {
  BIND(wdt:P361 AS ?parte_de)
  ?parte ?parte_de ?todo .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
LIMIT 40000
```
[Ejecución en Wikidata](https://w.wiki/Dan4)
:::::

### Faceta

De las relaciones _"subclase_de"_ y _"parte_de"_ se espera conceptualmente que sean transitivas: si "X es parte de Y" y "Y es parte de Z" se desearía poder afirmar que "X es parte de Z". Pero hay relaciones entre conceptos más laxas formalmente. Por ejemplo, se podría afirmar que el concepto de 'contaminación' es una de las facetas del concepto "vida urbana". Uno es una perspectiva acotada del otro, está relacionado desde un cierto punto de vista.
    
La siguiente consulta recupera enunciados de Wikidata en que se afirma que el sujeto es 'faceta de' (P1269) el objeto.    
    
:::::{hint} Consulta: enunciados que declaran 'ser faceta de' algo
:label: query-todas-faceta-de
:class: dropdown
:open:
```SPARQL
SELECT ?item1 ?item1Label ?faceta_de ?item2 ?item2Label
WHERE {
  BIND(wdt:P1269 AS ?faceta_de)
  ?item1 ?faceta_de ?item2 .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}  
LIMIT 40000 
```
[Ejecución en Wikidata](https://w.wiki/DanT)
:::::

## Clases y subclases en Wikidata
Hay casi cien mil clases utilizadas en Wikidata hasta la fecha. No se declaran formalmente como tal: la clase X lo es porque existe un ítem que pertenece (P31) a la misma, no porque exista un enunciado "X rdf:type rdfs:Class".

### Clases y sus elementos
La siguiente consulta devuelve todas clases utilizadas en Wikidata junto a los elementos que pertenecen a la clase.

:::::{hint} Consulta: todas las clases en Wikidata con su número de elementos
:label: query-todas-clases-y-recuento-elementos
:class: dropdown
:open:
```SPARQL

SELECT ?clase (COUNT(?item) AS ?numElementos)
WHERE {
  ?item wdt:P31 ?clase.
}
GROUP BY ?clase
ORDER BY DESC(?numElementos)
```
[Ejecución en Wikidata](https://w.wiki/Dare)
:::::

### Pares (subclase, clase)
La siguiente consulta devuelve todos los pares de clases (A,B) donde A es subclase de B. Sobre las cien mil clases en Wikidata aparecen casi tres millones y medio de relaciones de (subclase, clase).

:::::{hint} Consulta: todos los pares (clase1 subclase clase2)
:label: query-todas-clases-y-sus-superclases
:class: dropdown
:open:
```SPARQL
SELECT ?clase ?claseSup 
WHERE {
  ?clase wdt:P279 ?claseSup . 
}
```
[Ejecución en Wikidata](https://w.wiki/6VkN)
:::::

## Inferencia sobre subclases
La pertencia explícita de una instancia _I_ a una clase _C_ en RDF asegura que se puede deducir la pertenencia de I a cualquiera de las superclases donde _C_ esté transitivamente incluida. Es decir, si _C_ es una sub/sub/.../subclase de _Vertebrados_, de cualquier instancia de _C_ se puede deducir que pertenece a esta última clase, incluso aunque ese enunciado no conste explícitamente en la base de datos.

Esta inferencia estándar RDFS no se facilita en Wikidata.

### Inferencia en RDF
En RDFS se definen varias reglas, que son las que permiten asegurar inferencias como la anteriormente expuesta:

*La pertenencia (rdf:type) se transmite de subclase (rdfs:subClassOf) a superclase*.
   
$$
\left\rvert\begin{array}{ccc}
\text{ <elem>} &  \text{<rdf:type>} & \text{<C1> } \\
\text{ <C1>} &  \text{<rdfs:subClassOf>} &  \text{<C2> }     
\end{array}\right\rangle
\Rightarrow \text{ <elem> <rdf:type> <C2>}$$

*La inclusión es transitiva y genera una jerarquía de clases*.
    
$$
\left\rvert\begin{array}{ccc}
\text{<C1>} &  \text{<rdfs:subClassOf>} & \text{<C2> } \\
\text{<C2>} &  \text{<rdfs:subClassOf>} &  \text{<C3> }     
\end{array}\right\rangle
\Rightarrow \text{ <C1> <rdfs:subClassOf> <C3>}$$

    
### Inferencia en Wikidata
Wikidata no activa reglas similares para P31 y P279. No obstante, se puede forzar explícitamente en consultas SPARQL el retorno de resultados en una forma similar a los que se esperarían por inferencia. Para estas consultas se utilizan los 'Path Properties' de SPARQL: las propiedades compuestas como caminos de propiedades.  
    
    
La siguiente consulta devuelve todas las subclases propias de _Pintura_ (Q3305213), no importa a qué nivel se encuentren.

:::::{hint} Consulta: todas las subclases, a cualquier nivel, de *Pintura*
:label: query-todas-subclases-de-pintura
:class: dropdown
:open:
```SPARQL
SELECT ?C ?CLabel
WHERE {
  ?C wdt:P279+ wd:Q3305213 .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
```
[Ejecución en Wikidata](https://w.wiki/6Vxf)
:::::
   
La consulta anterior se puede refinar para facilitar, de cada una de esas subclases debajo de _Pintura_, su superclase inmediata.

:::::{hint} Consulta: todas las subclases, a cualquier nivel, de *Pintura* (2)
:label: query-todas-subclases-de-pintura2
:class: dropdown
:open:
```SPARQL
SELECT ?Csub ?CsubLabel ?Csuper ?CsuperLabel
WHERE {
  ?Csub wdt:P279+ wd:Q3305213 .
  ?Csub wdt:P279 ?Csuper .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}  
```
[Ejecución en Wikidata](https://w.wiki/6fET)
:::::

  
La siguiente consulta devuelve todas las instancias de todas las subclases de _Pintura_.

:::::{hint} Consulta: todas las instancias de todas las subclases, a cualquier nivel, de *Pintura*
:label: query-todas-instancias-de-subclases-de-pintura
:class: dropdown
:open:
```SPARQL
SELECT ?elem ?clase
WHERE {
  ?elem wdt:P31/wdt:P279* wd:Q3305213 .
  ?elem wdt:P31 ?clase .
}
ORDER BY ?clase
```
[Ejecución en Wikidata](https://w.wiki/6Vza)
:::::

