---
title: "geocodificación"
author: "Julián david Ruiz Herrera"
date: "`r Sys.Date()`"
output: 
  rmdformats::material:
    cards: false
---

```{r setup, include=FALSE, fig.align='center'}
knitr::opts_chunk$set(echo = TRUE)
```

```{r, include=FALSE}
library(rmdformats)
```

# proposito
El siguiente infrome busca hacer una confrontación entre diferentes fuentes de informacion para geolocalizar direcciones de medellín.

Con la fuente de información "Catastral" solo encontró 8 ubicaciones.<br>
Con la fuente de información "instalaciones de energia" solo encontró 8 ubicaciones.<br>
Con la fuente de información "malla vial" solo encontró 14 ubicaciones.<br>
Con la fuente de información "direccion más cercana" solo encontró 17 ubicaciones.<br> Con la fuente de información "toponimia" no encontró ubicaciones.

Por consiguiente la fuente que aporta más información es la de dirección más cercana seguida de "malla vial".

# comparación entre geo y geo_osm.
```{r}
library(readxl) # Lectura de archivos xlsx
library(tmaptools) # Geocodificación y visualización
library(tidygeocoder) # Geocodificación
library(leaflet) # Visualización 
library(tidyverse) # Manipulación de datos y visualización
library(sf) # Manejo de archivos vectoriales
```

```{r}
direcciones <- read_excel('direcciones.xlsx')
head(direcciones)
```
```{r}
dir <- with(direcciones, paste(Direcciones, Ciudad, Pais, sep = ', '))
```

```{r}
osm <- geocode_OSM(dir)
osm
```
```{r}
mapa <- geo(address = dir, method = 'arcgis')
geo(address = dir, method = 'arcgis')
```

Como resultado de lo anterior se da a entender que la funciión geo es más eficiente para encontrar la direcciones especificas de este data set.

```{r}
mapabase <- leaflet()%>% 
  addTiles()%>%
  
  addCircleMarkers(lng = mapa$long, lat = mapa$lat,color = "blue" 
                   , radius = 5, popup = paste("geo<br>",
                                 mapa$lat,
                                 mapa$long),opacity = 1) %>%
  addCircleMarkers(lng = osm$lon, lat = osm$lat,color = "red"
                   , radius = 5,popup = paste("geo_osm<br>",
                                 osm$lat,
                                 osm$lon),opacity = 1)%>%
  addLegend(labels = "<b>Ubicaciones</b>",colors = "", position = "bottomleft")
mapabase%>%
  addLegend(labels=c("geo","geo_oms"),colors= c("blue","red"),position = "topright")
```
La precisión de la herramienta geo no es muy buena puesto que dió como resultado direcciones en sanantonio de prado, las cuales nunca se dieron y que se identifican como:

```{r}
mapa[mapa$lat==6.19245343281618|mapa$lat==6.16980924401484|mapa$lat==6.17399575735215,]
```
CL 57 #58b 15, Medellín, Colombia es por los lados del mirador de calasanz,
CR 70 c 3 43, Medellín es un restaurante que es dificil de encontrar y se llama mondongos la 70, y CR 74 48010 es el estadio atanasio girardot.

# Comparación con los datos obtenidos de geomedellin.
```{r}
direccionesgeo <- read_excel('direcciones_geo.xlsx')
nuevo<-direccionesgeo[!(direccionesgeo$tipo=="No Ubicada"),]
```

```{r}
mapabase%>%
  addCircleMarkers(lng = as.numeric(nuevo$longitud), 
                   lat = as.numeric(nuevo$latitud),
                   color = "black",
                   radius = 5,
                   popup = paste("geomedellin<br>",
                                 nuevo$latitud,
                                 nuevo$longitud),opacity = 1)%>%
  addCircleMarkers(lng = c(-75.5898517100458 , -75.59138730814526), 
                   lat = c(6.257291432330994 , 6.2748540252374685),
                   color = "green",
                   radius = 6,
                   popup = paste("agregado",
                                 c( -75.5898517100458 , -75.59138730814526),
                                 c(6.257291432330994 , 6.2748540252374685)),opacity = 1)%>%
  addLegend(labels=c("geo 20","geo_oms 11","geomedellin 18","puntos agregados 2"),colors= c("blue","red","black","green"),position = "topright")

```
De lo anterior se puede intuir que las herramientas dadas para geolocalización en medellin son poco precisas y puede dar resultados muy errados si no se procede con precaución.






