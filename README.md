# *"Si estás viendo esto, significa que ya escuchaste todas las canciones atrás en el disco (...)"*

-- KidVoodoo, en "La explicación"

## Introducción

Para un ramo de Medición y Análisis dimencional de datos políticos, se nos pidió realizar una tarea con la finalidad de practicar habilidades de manipulación de datos y visualización en R. Ante esto, el siguiente scrip busca exponer las canciones más populares del artista **Kid Voodoo** utilizando la API de Spotify. , creando un gráfico que muestre la **popularidad** de sus canciones y si contienen o no contenido explícito.

El proceso se dividirá en cuatro etapas:\
1. **Ordenar y obtener los datos** desde Spotify.\
2. **Transformar** la información para quedarnos con las variables relevantes.\
3. **Modelar y visualizar** los resultados con `ggplot2`.\
4. **Comunicar** los hallazgos de forma atractiva.

## Instalación y carga de librerías

Primero instalamos y cargamos las librerías necesarias

``` r
library(spotifyr)
library(tidyverse)
library(jpeg)
library(grid)
```

## Conexión con la API de Spotify

Antes de comenzar, necesitamos generar un token de acceso con nuestras credenciales de Spotify Developer.

``` r
Sys.setenv(SPOTIFY_CLIENT_ID = '6927007fbc2c4447b7c46c3b9396fb07')
Sys.setenv(SPOTIFY_CLIENT_SECRET = '0114ac183ba2435386492c17b2ed0163')

access_token <- get_spotify_access_token()

access_token
```

## Búscamos al artista y extraemos los datos

Buscamos al artista Kid Voodoo y obtenemos sus canciones más populares.

``` r
kidpudu <- search_spotify("Kid Voodoo", type = "artist")
kidpudu$id
```

``` r
artist_id <- "10VBp06W8NIgMW4JruLCC4"
top_tracks <- get_artist_top_tracks(artist_id)
head(top_tracks)
```

## Transformación de datos

Nos quedamos con las variables que nos interesan:

1.  explicit → si la canción es explícita

2.  name → nombre de la canción

3.  popularity → nivel de popularidad (0 a 100)

``` r
df_kidpudu <- top_tracks |>
select(explicit, name, popularity)
```

*le coloqué pudu: porque es una forma meme de llamar al kidvoodoo, también se le dice kiddtuto, entre otros.*

## Visualización de datos

A continuación, creamos un gráfico de barras que muestra la popularidad de las canciones, diferenciando si son explícitas o no. Además, usamos una imagen de fondo y una paleta de colores personalizada.

``` r
img <- readJPEG("imagen de kidd.jpg")
g <- rasterGrob(img, width = unit(1,"npc"), height = unit(1,"npc"))




df_kidpudu |>
ggplot(aes(x = fct_reorder(name, popularity), y = popularity, fill = explicit)) +
annotation_custom(g, xmin = -Inf, xmax = Inf, ymin = -Inf, ymax = Inf) +
geom_col(alpha = 0.5) +
scale_fill_manual(values = c("TRUE" = "#f8f9fa", "FALSE" = "#6c757d"),
label = c("Sí", "No"),
name = "¿Es o no explícita?") +
scale_y_continuous(limits = c(0,100)) +
coord_flip() +
labs(
title = "Si estás viendo esto, significa que ya escuchaste todas las canciones atrás en el disco",
subtitle = "Canciones de Kidvoodoo según popularidad en Spotify",
x = "Nombre de canción",
y = "Popularidad (0-100)",
caption = "Fuente: API de Spotify"
) +
theme_minimal() +
theme(
plot.title = element_text(family = "Stencil", size = 14, face = "bold", hjust = 0.5, color = "white"),
plot.subtitle = element_text(family = "Segoe UI", size = 10, hjust = 0.5, color = "white"),
axis.title = element_text(family = "Segoe UI", size = 10, color = "white"),
axis.text = element_text(family = "Segoe UI", size = 10, color = "#adb5bd"),
legend.title = element_text(family = "Segoe UI", size = 10, color = "white"),
legend.text = element_text(family = "Segoe UI", size = 10, color = "white"),
legend.position = "bottom",
plot.background = element_rect(fill = "#202020", color = NA)
)
```

## ![](images/clipboard-4024435838)

## Conclusión

En este análisis aprendimos a conectar con la API de Spotify y a visualizar información musical de forma estética. Se observa que algunas de las canciones más populares de Kid Voodoo no son explícitas, aunque las más escuchadas tienden a tener un tono más crudo y personal. Esto refleja la dualidad en su música, que permite llegara públicos variados.
