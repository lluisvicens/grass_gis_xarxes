# Exercicis d'anàlisis de xarxes amb GRASS GIS

A continuació es mostren un seguit d'anàlisis relacionats amb xarxes, que es poden dur a terme amb GRASS GIS i amb el conjunt d'eines que es troben al paquet _**v.net**_.

## 1. La preparació de la xarxa

Abans de començar qualsevol exercici relatiu a l'anàlisi de xarxes, per senzill que aquest sigui, és precís preparar les dades amb les quals volem treballar. Per als casos d'ús que a continuació es motraran, s'utilitzaran tres capes:

- Xarxa de carreteres de la comarca de l'Alt Empordà. Font: [OSM](https://www.openstreetmap.org)
- Ubicació de tres plantes de gestió de residus ramaders. Font: Elaboració pròpia
- Ubicació d'un conjunt d'explotacions. Font: Elaboració pròpia

### 1.1 Preparant GRASS: Creació d'un mapset de treball, i importació de dades

- El primer pas, consisteix en crear un _location_ y un _mapset_ de GRASS GIS amb un sistema de referència **EPSG:25831**
- En acabat, procedim a importar les tres capes amb la comanda o eina **_v.import_**
- Carreguem i simbolitzem totes les capes

#### 1.2 Crear la xarxa: assignació de costos

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
- Amb aquestes senzilles passes ja tenim la xarxa preparada, degudament segmentada, i amb la taula d'atributs completa en la qual hem afegit un cap de velocitat màxima de la via, longitud de cadascun dels arcs, i el cost que suposa recórrer cadascun dels arcs.

#### 1.3 Crear la xarxa: connectar nodes a la xarxa

- A més de la capa de carreteres, disposem de dues capes de punts que voldrem connectar a la xarxa, i que representen les ubicacions de les plantes de gestió de les explotacions a les quals cal donar servei. El procés a seguir serà connectar les plantes de gestió a _layer 2_ de la xarxa (els arcs estan emmagatzemats al _layer 1_), i les explotacions al _layer 3_, i posteriorment vincularem les seves taules d'atributs.

- Accedim al menú Vector > Network Analysis > Network preparation. A la finestra emergent, dins la pestanya _Required_ seleccionem l'opció **connect**. A la pestanya _Arcs_ indiquem sobre quina capa volem treballar (**xarxa_clean**), a la pestanya _Nodes_ activem la casella **Snap points to network** i seleccionem la capa **plantes_gestio** verificant que es connectaran al nivell 2. Finalment activem la pestanya _Optional_ i donem un nom de sortida a la capa resultant (**xarxa_pas1**), i definim un llindar de connexió de **500** metres:
```sql
$ v.net -s input=xarxa_clean@xarxes points=plantes_gestio@xarxes output=xarxa_pas1 operation=connect arc_type=line threshold=500
```

- Repetim novament el pas anterior, però aquesta vegada seleccionant la capa **xarxa_pas1** com a capa d'entrada, la capa **explotacions** com a capa de nodes, connectant-la al nivell o _layer_ **3**, i amb el nom de sortida **xarxa_completa**:
```sql
$ v.net -s input=xarxa_pas1@xarxes points=explotacions@xarxes output=xarxa_completa operation=connect arc_type=line node_layer=3 threshold=500
```

- El següent pas, consistirà en vincular les taules d'atributs de les capes **plantes_gestio** i **explotacions** a la capa **xarxa_completa** als nivells **2** i **3**, respectivament. Per a dur a terme aquesta tasca cal obrir la taula d'atributs de la capa **xarxa_completa** i activar la pestanya _Manage layers_. En l'apartat Add layer seleccionem primerament la capa **plantes_gestio** i la vinculem al nivell 2 tot fent un clic al botó **Add layer**. Seguidament fem el mateix amb la capa **explotacions** i el nivell 3.

## 2. Anàlisis bàsics

Una vegada degudament preparada la xarxa amb la qual hem de dur a terme els diferents anàlisis de xarxes, i carregades les capes de les explotacions i de les plantes de gestió, que podem etiquetar amb el valor de la columna **[cat]**, començarem per veure els anàlisis més senzills que es poden dur a terme amb GRASS GIS.

### 2.1 Obtenció de la ruta més curta entre dos punts de la xarxa

#### Via panell GRASS GIS Network Analysis Tool
#### Via algoritme v.net.shortest

.. figure:: _static/test.jpg
   :align: center
   **Figura 1:** Configuración del SRS del proyecto de QGIS.

