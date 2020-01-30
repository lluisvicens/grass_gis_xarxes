# Exercicis d'anàlisis de xarxes amb GRASS GIS

A continuació es mostren un seguit d'anàlisis relacionats amb xarxes, que es poden dur a terme amb GRASS GIS i amb el conjunt d'eines que es troben al paquet _**v.net**_.

## 1. La preparació de la xarxa

Abans de començar qualsevol exercici relatiu a l'anàlisi de xarxes, per senzill que aquest sigui, és precís preparar les dades amb les quals volem treballar. Per als casos d'ús que a continuació es motraran, s'utilitzaran tres capes:

Xarxa de carreteres de la comarca de l'Alt Empordà. Font: [OSM](https://www.openstreetmap.org)
Ubicació de tres plantes de gestió de residus ramaders. Font: Elaboració pròpia
Ubicació d'un conjunt d'explotacions. Font: Elaboració pròpia

### 1.1 Preparant GRASS: Creació d'un mapset de treball, i importació de dades

- El primer pas, consisteix en crear un _location_ y un _mapset_ de GRASS GIS amb un sistema de referència **EPSG:25831**
- En acabat, procedim a importar les tres capes amb la comanda o eina **_v.import_**
- Carreguem i simbolitzem totes les capes

#### Creant la xarxa

- _**v.clean**_ per segmentar tots els arcs a cada intersecció:
```sql
  $ v.clean input=xarxa_cost@xarxes type=line output=xarxa_clean tool=break
```
- A continuació, crearem una nova columna amb nom **[velocitat]**, a la que assignarem una velocitat estàndard considerant la seva categoría. En primer lloc cal obrir la taula d'atributs de la capa **xarxa_clean**, i a l'apartat Add column, esciure el nom de la nova columna, especificant que serà de tipus enter , i fem un clic al botó **Add** per finalitzar.

- El següent pas consistirà en donar valor a aquesta columna, per a cada arc de la xarxa en funció de la seva categoria. Ens posem sobre el nom de la columna, fem botó dret, y seleccionem l'opció **Field calculator**. Dins l'apartat _Query_, escribim les següents sentències SQL:  

``` sql
UPDATE xarxa_clean SET velocitat = 120 WHERE  cat_via = 1
UPDATE xarxa_clean SET velocitat = 90 WHERE  cat_via = 2
UPDATE xarxa_clean SET velocitat = 50 WHERE  cat_via = 3
UPDATE xarxa_clean SET velocitat = 30 WHERE  cat_via = 4
```
- A continuació, cal crear una nova columna que durà per nom **[longitud]**, de tipus decimal (3), seguint el mateix procés anteriorment mostrat i, una vegada creada, fem un clic amb el botó dret del mouse sobre la capçalera i activem l'opció Calculate > Line length.

- Finalment, crearem una nova columna anomenada **[cost]**, de tipus decimal, que actualitzarem amb el següent valor de cost: el temps necessari per a recórrer un arc, en segons, donada la seva longitud i la velocitat màxima de la via. Així, una vegada creada la nova columna fem un clic sobre la capçalera per acabar seleccionant novament l'opció **Field calculator**.

```sql
UPDATE xarxa_clean SET cost =  (longitud  /  (velocitat  * 1000 / 3600))
```
- werw
