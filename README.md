# INE2021
Datos de no respuesta EPHPM
---
title: "INE Honduras,Datos de No Respuesta Encuesta de Hogares Honduras 2016-2019"
output: 
  flexdashboard::flex_dashboard:
    orientation: columns
    vertical_layout: fill
---


```{r setup, include=FALSE}
library(flexdashboard)
```

## No Respuesta


# Página 1


Introducción:

En los últimos años la Encuesta Permanente de Hogares de Propósitos múltiples (EPHPM) que realiza el Instituto Nacional de Estadística (INE) ha mostrado una baja cobertura de los segmentos de la muestra que son levantados en el campo. Mas conocidos como la No respuesta, es decir; cuando no se realizan todas las entrevistas esperadas.

Objetivo de la actividad:

Aplicar los conocimientos adquiridos en el Diplomado de R a fin de diseñar un dashboard que permita visualizar el tema de no respuesta en la EPHPM Honduras para los años 2016-2019.


# Página 2

```{r echo = FALSE, warning=FALSE, message=FALSE}
library(ggplot2) 
library(plotly)
library(readxl)
library(DT)
library(sae)
###### Para mapear
library(sf)
library(raster)
library(dplyr)
library(spData)
#library(spDataLarge)
library(tmap)    
library(leaflet)
library(mapview)
library(ggplot2) 
library(shiny)   
library(maptools)
library(gridExtra)
library(rgdal)
library(readr)
library(stringr)
library(tidyr)
library(kableExtra)
```

```{r include = FALSE, echo = FALSE, warning=FALSE, message=FALSE}
setwd("C:/Users/melil/Documents/dasboardejemplo")
HND<- readOGR(dsn="division_departamental_hn.shp", layer = "division_departamental_hn")
Mapa_HONDURAS<- fortify(HND)

No_RPTANAC <- read_excel("No_respuestanacional.xlsx")
No_RPTA2016 <-read_excel ("No_respuesta2016.xlsx")
No_RPTA2017 <-read_excel ("No_respuesta2017.xlsx")
No_RPTA2018 <-read_excel ("No_respuesta2018.xlsx")
No_RPTA2019 <-read_excel ("No_respuesta2019.xlsx")
No_RPTA20162019F <-read_excel ("No_respuesta serie16-19.xlsx")
```

```{r echo = FALSE, warning=FALSE, message=FALSE}
No_ResptaNAC<-No_RPTANAC %>%
group_by(Year_serie)%>%
summarise(median_No_Respta=mean(No_Respta))
```

## columna 1

### Tasas de no respuesta a nivel nacional 2016 - 2019

```{r echo = FALSE, warning=FALSE, message=FALSE}
No_ResptaNAC<-No_ResptaNAC%>%
mutate(Year_serie = factor(Year_serie,level=Year_serie))
```

```{r echo = FALSE, warning=FALSE, message=FALSE}

barra_baseNAC <- plot_ly(No_ResptaNAC, x = ~Year_serie, y = ~median_No_Respta, type = 'bar',
                        name = 'Tasa de no respuesta a nivel nacional 2016-2019',
               marker = list(color = 'rgb(158,202,225)',
                             line = list(color = 'rgb(8,48,107)'))) %>% 
  layout(title = "Tasa de No Respuesta a nivel nacional 2016-2019",
                      xaxis = list(title = "Año"),
                      yaxis = list(title = "Tasa promedio"),
         caption = "Fuente:Encuesta Permanente de Hogares, INE 2016-2019")
barra_baseNAC


```

 
# Página 3


## columna 1

### Tasas de no respuesta a nivel departamental 2016 - 2019
```{r}
No_RPTA20162019F %>% filter(Depto!="Nacional")%>% select(Year_serie,Depto,No_Respta) %>%filter(Depto!="GRACIAS A DIOS/a")%>% filter(Depto!="ISLAS DE LA BAHÍA/a")%>%filter(Depto!="GRACIAS A DIOS")%>% filter(Depto!="ISLAS DE LA BAHÍA")%>%
  arrange(Depto,Year_serie) %>% pivot_wider(names_from = Year_serie,values_from = No_Respta)%>%
  kable() %>%
  kable_styling()
```

# Página 4

## columna 1

```{r warning=FALSE, message=FALSE}
Mapa_HONDURAS <- full_join(Mapa_HONDURAS, No_RPTA2016%>% 
                              dplyr::select(Codepto, No_Respta) %>% 
                              mutate(id = as.character(Codepto))) %>% 
  mutate(No_Respta = ifelse(is.na(No_Respta), 0, No_Respta), Codepto = NULL)

Mapa <- ggplot() +
  geom_polygon(data = Mapa_HONDURAS, aes(long, lat, group=group, fill= No_Respta),
               colour ="black", size = 0.005) +
   coord_quickmap() +
  theme_void() +
  labs(fill = "NO RESPUESTA", title="Mapa no respuesta, año 2016")

Mapa

```

## Columna 2

```{r echo = FALSE, warning=FALSE, message=FALSE}
No_Respta2016<-No_RPTA2016 %>% filter(Depto!="GRACIAS A DIOS/a" | Depto!="ISLAS DE LA BAHÍA/a") %>%
group_by(Depto)%>%
summarise(median_No_Respta=mean(No_Respta))

barra_base16<-No_Respta2016 %>%filter(Depto!="GRACIAS A DIOS/a")%>% filter(Depto!="ISLAS DE LA BAHÍA/a")%>%
  arrange(desc(median_No_Respta))%>%
  mutate(Depto = factor(Depto,level=Depto))

barra_base16 <- plot_ly(No_Respta2016, x = ~Depto, y = ~median_No_Respta, type = 'bar',
                        name = 'Tasa de no respuesta',
               marker = list(color = 'rgb(58,200,225)',
                          line = list(color = 'rgb(8,48,107)',
                                         width = 1.5))) %>% 
  layout(title = "Tasa de No Respuesta 2016",
                      xaxis = list(title = "Año"),
                      yaxis = list(title = "Tasa promedio"),
         caption = "Fuente:Encuesta Permanente de Hogares, INE 2016")
barra_base16

```


# Página 5

## columna 1

```{r warning=FALSE, message=FALSE}
Mapa_HONDURAS <- full_join(Mapa_HONDURAS, No_RPTA2017%>% 
                              dplyr::select(Codepto, No_Respta) %>% 
                              mutate(id = as.character(Codepto))) %>% 
  mutate(No_Respta = ifelse(is.na(No_Respta), 0, No_Respta), Codepto = NULL)

Mapa <- ggplot() +
  geom_polygon(data = Mapa_HONDURAS, aes(long, lat, group=group, fill= No_Respta),
               colour ="black", size = 0.005) +
   coord_quickmap() +
  theme_void() +
  labs(fill = "NO RESPUESTA", title="Mapa no respuesta, año 2017")+
  scale_fill_gradientn(colours=c("#FF0000","#C80815","#D90115","#FF2400","#800200")) 

Mapa

```

## Columna 2

```{r warning=FALSE, message=FALSE}
No_Respta2017<-No_RPTA2017 %>% filter(Depto!="GRACIAS A DIOS" | Depto!="ISLAS DE LA BAHÍA") %>%
group_by(Depto)%>%
summarise(median_No_Respta=mean(No_Respta))

barra_base17<-No_Respta2017 %>%filter(Depto!="GRACIAS A DIOS/a")%>% filter(Depto!="ISLAS DE LA BAHÍA/a")%>%
  arrange(desc(median_No_Respta))%>%
  mutate(Depto = factor(Depto,level=Depto))

barra_base17 <- plot_ly(No_Respta2017, x = ~Depto, y = ~median_No_Respta, type = 'bar',
                        name = 'Tasa de no respuesta',
               marker = list(color = 'rgba(222,45,38,0.8)',
                             line = list(color = 'rgba(204,204,204,1)',
                                         width = 1.5))) %>% 
  layout(title = "Tasa de No Respuesta 2017",
                      xaxis = list(title = "Año"),
                      yaxis = list(title = "Tasa promedio"),
         caption = "Fuente:Encuesta Permanente de Hogares, INE 2016")
barra_base17
```

# Página 6

## columna 1

```{r warning=FALSE, message=FALSE}
Mapa_HONDURAS <- full_join(Mapa_HONDURAS, No_RPTA2018%>% 
                              dplyr::select(Codepto, No_Respta) %>% 
                              mutate(id = as.character(Codepto))) %>% 
  mutate(No_Respta = ifelse(is.na(No_Respta), 0, No_Respta), Codepto = NULL)

Mapa <- ggplot() +
  geom_polygon(data = Mapa_HONDURAS, aes(long, lat, group=group, fill= No_Respta),
               colour ="black", size = 0.005) +
   coord_quickmap() +
  theme_void() +
  labs(fill = "NO RESPUESTA", title="Mapa no respuesta, año 2018")+
  scale_fill_gradientn(colours=c("#228B22","#00A86B","#50C878","#01D758","#006400")) 

Mapa

```

## Columna 2

```{r warning=FALSE, message=FALSE}
No_Respta2018<-No_RPTA2018 %>% filter(Depto!="GRACIAS A DIOS" | Depto!="ISLAS DE LA BAHÍA") %>%
group_by(Depto)%>%
summarise(median_No_Respta=mean(No_Respta))

barra_base18<-No_Respta2018 %>%filter(Depto!="GRACIAS A DIOS/a")%>% filter(Depto!="ISLAS DE LA BAHÍA/a")%>%
  arrange(desc(median_No_Respta))%>%
  mutate(Depto = factor(Depto,level=Depto))

barra_base18<- plot_ly(No_Respta2018, x = ~Depto, y = ~median_No_Respta, type = 'bar',
                        name = 'Tasa de no respuesta',
               marker = list(color = 'rgb(153, 50, 204)',
                             line = list(color = 'rgb(47, 79, 79)',
                                         width = 1.5))) %>% 
  layout(title = "Tasa de No Respuesta 2018",
                      xaxis = list(title = "Año"),
                      yaxis = list(title = "Tasa promedio"),
         caption = "Fuente:Encuesta Permanente de Hogares, INE 2018")
barra_base18
```

# Página 7

## columna 1

```{r warning=FALSE, message=FALSE}
Mapa_HONDURAS <- full_join(Mapa_HONDURAS, No_RPTA2019%>% 
                              dplyr::select(Codepto, No_Respta) %>% 
                              mutate(id = as.character(Codepto))) %>% 
  mutate(No_Respta = ifelse(is.na(No_Respta), 0, No_Respta), Codepto = NULL)

Mapa <- ggplot() +
  geom_polygon(data = Mapa_HONDURAS, aes(long, lat, group=group, fill= No_Respta),
               colour ="black", size = 0.005) +
   coord_quickmap() +
  theme_void() +
  labs(fill = "NO RESPUESTA", title="Mapa no respuesta, año 2019")+
  scale_fill_gradientn(colours=c("#7B3F00","#88421D","#883801","#6D351A","#964B00")) 

Mapa

```

## Columna 2

```{r warning=FALSE, message=FALSE}
No_Respta2019<-No_RPTA2019 %>% filter(Depto!="GRACIAS A DIOS" | Depto!="ISLAS DE LA BAHÍA") %>%
group_by(Depto)%>%
summarise(median_No_Respta=mean(No_Respta))
  

barra_base19<-No_Respta2019 %>%filter(Depto!="GRACIAS A DIOS/a")%>% filter(Depto!="ISLAS DE LA BAHÍA/a")%>%
  arrange(desc(median_No_Respta))%>%
  mutate(Depto = factor(Depto,level=Depto))

barra_base19 <- plot_ly(No_Respta2019, x = ~Depto, y = ~median_No_Respta, type = 'bar',
                        name = 'Tasa de no respuesta', text = 'Tasa',
               marker = list(color = 'rgb(250, 250, 210)',
                             line = list(color = 'rgb(32, 178, 170)',
                                         width = 1.5))) %>% 
  layout(title = "Tasa de No Respuesta 2019",
                      xaxis = list(title = "Año"),
                      yaxis = list(title = "Tasa promedio"),
         caption = "Fuente:Encuesta Permanente de Hogares, INE 2019")
barra_base19
```

