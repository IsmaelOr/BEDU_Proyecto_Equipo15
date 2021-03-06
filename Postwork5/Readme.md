# Postwork Sesi贸n 5

## :dart: Objetivo

- Continuar con el desarrollo de los postworks; en esta ocasi贸n se utiliza la funci贸n `predict` para realizar predicciones de los resultados de partidos para una fecha determinada

## 馃敡 Requisitos

- Haber desarrollado los postworks anteriores
- Cubrir los temas del prework
- Replicar los ejemplos de la sesi贸n

## 馃 Desarrollo

#### 1. A partir del conjunto de datos de soccer de la liga espa帽ola de las temporadas 2017/2018, 2018/2019 y 2019/2020, crea el data frame `SmallData`, que contenga las columnas `date`, `home.team`, `home.score`, `away.team` y `away.score`; esto lo puede hacer con ayuda de la funci贸n `select` del paquete `dplyr`. Luego establece un directorio de trabajo y con ayuda de la funci贸n `write.csv` guarda el data frame como un archivo csv con nombre *soccer.csv*. Puedes colocar como argumento `row.names = FALSE` en `write.csv`. 

El conjunto de datos de las temporadas 2017 al 2020 lo tenemos almacenado en `Soccer` por lo que lo mandamos llamar retirando la columna FTR que no ser谩 necesaria y asignandolo a `SmallData` para posteriormente ordenar las columnas.
```R
SmallData <- select(Soccer, -FTR)
SmallData <- select(SmallData, Date, HomeTeam, FTHG, AwayTeam ,FTAG)
summary(SmallData)

```
De lo anterior obtenemos el siguiente resumen de `SmallData`
<p align = "center">
  <img src = "https://github.com/IsmaelOr/BEDU_Proyecto_Equipo15/blob/main/Imagenes/Postwork5/smallData_summary.png">
</p>



Verificando que la estructura del data frame es la adecuada usamos la funci贸n `colnames` para renombrar las columnas a como lo requiere `fbRanks`, con los datos en el orden y con el nombre que requerimos lo guardamos en un nuevo archivo csv. 
```R
colnames(SmallData) <- c("date", "home.team", "home.score", "away.team", "away.score")

write.csv(SmallData, file = "Soccer.csv", row.names = F)
```

#### 2. Con la funci贸n `create.fbRanks.dataframes` del paquete `fbRanks` importe el archivo *soccer.csv* a `R` y al mismo tiempo asignelo a una variable llamada `listasoccer`. Se crear谩 una lista con los elementos `scores` y `teams` que son data frames listos para la funci贸n `rank.teams`. Asigna estos data frames a variables llamadas `anotaciones` y `equipos`.

Instalamos el paquete fbRanks y cargamos la librer铆a,  `create.fbRanks.dataframes`  es una funci贸n auxiliar que lee archivos .csv para crear las puntuaciones, team.resolver y equipos data.frames. Su estructura es la siguiente:
`create.fbRanks.dataframes(scores.file, team.resolver=NULL, teams.file=NULL, date.format="%Y-%m-%d", na.remove=FALSE) `
El argumento que nos interesa es el de scores.file que es un archivo o vector de nombres de archivo (archivos csv) que tienen los datos de los partidos.
Debe tener columnas denominadas "date", "home.team", "home.score" y "away.team", "away.score".
Se pueden agregar columnas adicionales, p. Ej. "surface" o "attack.adv" (local, visitante, neutral), y estos se pueden utilizar como variables de respuesta.

Esta funci贸n devuelve una lista con los elementos `scores` y `teams` que son data frames de datos listos para usarse en la funci贸n de `rank.teams`
Estos elementos los asignamos a las variables 鈥渁notaciones鈥? y 鈥渆quipos鈥?..

```R
install.packages("fbRanks")
library(fbRanks)

listaSoccer <- create.fbRanks.dataframes(scores.file = "Soccer.csv")

anotaciones <- listaSoccer$scores
equipos <- listaSoccer$teams

```
El resultado es una lista conformada por 4 dataframes y 2 valores l贸gicos como podemos ver en la imagen: 
<p align = "center">
  <img src = "https://github.com/IsmaelOr/BEDU_Proyecto_Equipo15/blob/main/Imagenes/Postwork5/listasoccer.png">
</p>


#### 3. Con ayuda de la funci贸n `unique` crea un vector de fechas (`fecha`) que no se repitan y que correspondan a las fechas en las que se jugaron partidos. Crea una variable llamada `n` que contenga el n煤mero de fechas diferentes. Posteriormente, con la funci贸n `rank.teams` y usando como argumentos los data frames `anotaciones` y `equipos`, crea un ranking de equipos usando 煤nicamente datos desde la fecha inicial y hasta la pen煤ltima fecha en la que se jugaron partidos, estas fechas las deber谩 especificar en `max.date` y `min.date`. Guarda los resultados con el nombre `ranking`.

Usando la funci贸n `unique` creamos un vector de fechas que no se repitan de las fechas en las que se jugaron partidos, y con `lenght` obtenemos el tama帽o del vector lo que posteriormente nos ayudar谩 al seleccionar las fechas como posiciones. 

Como se hab铆a comentado los datos de listaSoccer ten铆an el formato para usarse en `rank.teams` que crea rankings usando dataframes de los registros de partidos a trav茅s de la regresi贸n de Poission.

```R
fecha <- unique(SmallData$date)
n <- length(fecha)

ranking <- rank.teams(anotaciones,equipos, 
                      max.date = (max(fecha)-1) , min.date = min(fecha), 
                      date.format = '%d/%m/%Y')
```
De esto obtenemos los siguientes Rankings para los partidos jugados del 18 de agosto del 2017 al 22 de diciembre del 2020:
<p align = "center">
  <img src = "https://github.com/IsmaelOr/BEDU_Proyecto_Equipo15/blob/main/Imagenes/Postwork5/ranking.PNG">
</p>

#### 4. Finalmente estima las probabilidades de los eventos, el equipo de casa gana, el equipo visitante gana o el resultado es un empate para los partidos que se jugaron en la 煤ltima fecha del vector de fechas `fecha`. Esto lo puedes hacer con ayuda de la funci贸n `predict` y usando como argumentos `ranking` y `fecha[n]` que deber谩 especificar en `date`.

Predict es una funci贸n de fbRanks que predice el resultado (ganar, perder o empatar) y los goles para los partidos , se le da como argumento la lista de ranking y como fecha para aprendizaje le damos la 煤ltima del vector. Los resultados obtenidos son los siguientes:
<p align = "center">
  <img src = "https://github.com/IsmaelOr/BEDU_Proyecto_Equipo15/blob/main/Imagenes/Postwork5/prediction.png">
</p>
