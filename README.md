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
```
  $ v.clean input=xarxa_cost@xarxes type=line output=xarxa_clean tool=break
```
- A continuació, crearem una nova columna amb nom **[velocitat]**, a la que assignarem una velocitat estàndard considerant la seva categoría:


