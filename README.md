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

Antes de comenzar, necesitamos generar un token de acceso con nuestras credenciales de Spotify Developer, estos datos son secretos, entonces los digitaré con "x".

``` r
Sys.setenv(SPOTIFY_CLIENT_ID = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx')
Sys.setenv(SPOTIFY_CLIENT_SECRET = 'xxxxxxxxxxxxxxxxxxxxxxxxxx')

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

*le coloqué "pudu", porque es una forma meme de llamar al kidvoodoo, también se le dice kidtuto, entre otros.*

## Visualización de datos

A continuación, creamos un gráfico de barras que muestra la popularidad de las canciones, diferenciando si son explícitas o no. Además, usamos una imagen de fondo y una paleta de colores personalizada.

\*Explicaciones específicas\*

1.  Se eligió una tipografía relacionada a la estética de los últimos álbum del Kid Voodoo.
2.  El título es un audio que subió como parte final de su álbum.
3.  Los colores que se usaron son parte de la paleta de su álbum: blanco, negro y grises. Además, de esta forma resalta los gráficos con el fondo de atrás.
4.  Lo más interesante de esta visualización de datos, es que se puede ver si las populares son explícitas o no.

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

En este análisis aprendimos a conectar con la API de Spotify y a visualizar información musical de forma estética. Se observa que algunas de las canciones más populares de Kid Voodoo no son explícitas, aunque de todas formas logramos ver que en su top 10 de canciones, la mitad son explícitas y las otras no. De todas formas, en su top 5, 3 no son explícitas y 2 sí lo son. Esto refleja la dualidad en su música, que permite llegar a públicos variados.
