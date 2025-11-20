# Tarea_02

*Análisis de matrícula y rendimiento académico en Chile: Parte 1*

- Tarea 2 para el curso ICP5006-1 "Medición y análisis dimensional de datos políticos". En este, se realiza una caracterización descriptiva del sistema escolar 2024, analizando matrícula, modalidades y resultados académicos mediante tablas y visualizaciones estadísticas.
- Autora: María Gracia Abbott

# 0. Introducción

Con la intención de realizar una tarea más alineada con los intereses académicos y profesionales de la autora, esta tarea tiene como objetivo realizar un análisis descriptivo inicial de la base de datos de *Rendimiento académico por estudiante 2024* del Centro de Estudios del Mineduc. Esto con el fin de caracterizar la matrícula escolar y explorar patrones relevantes según tipo de dependencia, niveles de enseñanza, modalidades de educación media y situación final de los estudiantes. 

Para ello, primero se realiza un proceso de limpieza y estandarización de la base, asegurando la codificación deseada de variables y la creación de categorías analíticas necesarias. Posteriormente, se desarrollan distintos análisis de frecuencia y visualizaciones que permiten describir la composición del sistema educativo según dependencia administrativa, nivel y curso, modalidad HC/TP, y resultados finales del año escolar.


## 0.1 Librerías necesarias


```{r}
library(tidyverse) #manipular datos
library(stringr) #limpiar textos
library(stringi) #limpiar textos
library(gt) #generar tablas
library(ggplot2) #generar gráficos
library(scales) #generar gráficos
```


# 1. Base de datos
La base de datos original que se utilizará en esta ocasión corresponde a la de "Rendimiento académico por estudiante" del año 2024. Esta se encuentra disponible en la página de los Datos Abiertos del Centro de Estudios del Mineduc (Chile). Esta se descarga en formato CSV, teniendo una codificación UTF-8 (aparentemente es importante saber este dato, por lo que lo dejo aquí). Al ser muy pesada, tristemente no me dejó adjuntarla en este repositorio, por lo que adjunto el link que permite acceder directamente a la página: https://datosabiertos.mineduc.cl/rendimiento-por-estudiante-2/

Junto con la base se descargará el libro de códigos y un excel con tabulaciones de ciertos datos. Ambos se encuentran en la carpeta de data disponibles en este repositorio. La base contiene originalmente 3.568.930 observaciones (estudiantes) y 45 variables disponibles. Al filtrar en establecimientos funcionando queda en 3.568.887 observaciones. Asimismo, al eliminar los duplicados, y dejar únicamente una observación por estudiante, el número final de observaciones es de 3.329.072.

Las variables utilizadas inicialmente corresponden a: `"MRUN"`, `"ESTADO_ESTAB"`, `"NOM_REG_RBD_A"`, `"NOM_COM_ALU"`, `"RURAL_RBD"`, `"COD_DEPE2"`, `"COD_ENSE2"`, `"COD_GRADO"`, `"GEN_ALU"`, `"PROM_GRAL"`, `"ASISTENCIA"`, `"SIT_FIN_R"`. Sin embargo, tanto `MRUN` como `ESTADO_ESTAB` se utilizaron unicamente para hacerl el filtro inicial, por lo que el número final son 10 variables. Al final del paso 1.2 se reemplaza la variable `curso` por `nivel_curso` construida en base a la misma y a `nivel_enseñanza`. 

Para conocer el proceso de creación y limpieza de la base, les invito a revisar el script ubicado en `01_script`.

Sin embargo, dejaré el código para importar la base ya lista, disponible en la carpeta `00_data`:

## 1.1 Importar nuevamente la base de datos

Acá, en caso de tener que cerrar y volver en otro momento, dejo el código para volver a importar la base de datos de manera que la lea correctamente. Me pasó que muchas veces volvía a importarse tanto los promedios como la asistencia como character y no como numeric. Este código se asegura de que eso no pase (espero).

```{r}
rendimiento_2024_final <- read_csv("00_data/rendimiento_2024_final.csv", 
    col_types = cols(promedio = col_number(), 
        porc_asistencia = col_number()))

rm(rendimiento_2024_final)
```

# 2. Análisis de datos

En esta tarea se buscó realizar un análisis inicial de la base de datos presentada. Se analizaron la matrícula, el rendimiento académico y la asistencia según nivel educativo y tipo de dependencia. El estudio describe además la distribución de estudiantes por cursos y modalidades en educación media.

## 2.1 Cantidad de estudiantes según dependencia 

En esta sección se describe la distribución de los estudiantes según el tipo de dependencia administrativa de sus establecimientos durante 2024. De esta manera, ofrece una visión general de la composición del sistema educativo, identificando que la mayoría de los estudiantes  (53,5%) asisten a un establecimiento particular subvencionado. Seguido de los establecimientos municipales que concentran el 29,6% de los estudiantes. Se da un salto de más de 20 puntos porcentuales a los establecimientos particulares que concentran solamente a un 9,1% de los escolares, seguidos de los servicios locales de educación con 6,5% y finalmente las corporaciones de administración delegada con 1,4%. Tal y como se logra apreciar en la siguiente tabla y gráfico:



## 2.2 Cantidad de estudiantes por curso (tabla de frecuencia)

En esta sección se presenta la distribución del estudiantado chileno según su curso, permitiendo identificar que cursos son especialmente numerosos y cuales cuentan con una baja matrícula. Se releva que las tendencias son bastante similares entre todos los cursos, representando cada uno entre 6-8% del total. Puede parecer que los cursos de media tienen menor matricula pero eso es porque se presentan desagregados entre modalidad Humanista-Científica (HC) y Técnico Profesional (TP). De manera interesante, 1° Básico y 4° Medio (en su totalidad) representan el mismo porcentaje de las matrículas (6,8%). Esto se puede evidenciar en la siguiente tabla y gráfico:


## 2.3 Cantidad de estudiantes por curso según modalidad (HC o TP)

Este análisis responde al deseo de comparar la matrícula de Enseñanza Media entre las modalidades HC y TP, distinguiendo cada curso de 1° a 4° Medio. A través de este se buscaba identificar tendencias de preferencia, distribución de oferta educativa y diferencias relevantes en tamaño de grupos entre ambas modalidades. 

De esta manera, se logra evidenciar las grandes diferencias entre la matrícula de estudiantes en las distintas modalidades, superando con creces la HC a la TP. La brecha es mayor en 1° y 2° medio (aproximadamente 70 puntos porcentuales) que en 3° y 4° medio (40,6 pp y 30 pp respectivamente). Esto tiene sentido al enfocarse la mayor oferta educativa de TP en estos dos cursos, siendo todavía escasos e inusuales los establecimientos que ofrecen TP desde 1° Medio. 

Aún así, la diferencia entre los estudiantes que estudian una modalidad Científico-Humanista y aquellos en Técnico-Profesional es extensa. Sería interesante para un análisis posterior comparar la tendencia a lo largo de los años de esta e intentar compararla con las tendencias regionales.


## 2.4 Situación final según tipo de dependencia administrativa

Este análisis examina el rendimiento final de los estudiantes según dependencia administrativa, identificando diferencias en las tasas de promoción, repetición, retiro o traslado. De esta manera se releva que el tipo de dependencia con mayor tasa de promoción es el particular pagado, mientras que el de menor tasa es la corporación de administración delegada. Esta tendencia se repite de manera inversa al revisar las tasas de reprobación, teniendo la corporación de administración delegada el porcentaje más alto de sus estudiantes en aquella situación entre los distintos tipos de dependencia (6,74%).

# 3. Conclusión

A modo de conclusión, esta tarea permitió caracterizar clara y sistemáticamente la distribución del estudiantado chileno durante 2024 y sus principales indicadores académicos. Los análisis evidencian que la mayor parte de la matrícula se concentra en establecimientos particulares subvencionados y que las distribuciones por curso son relativamente estables. Asimismo, se observan diferencias significativas entre modalidades HC y TP, así como variaciones en la situación final de los estudiantes según dependencia administrativa. Estos resultados ofrecen un primer acercamiento cuantitativo a la estructura del sistema escolar, sentando una base sólida para análisis posteriores más específicos o comparativos. Estos, cabe recalcar, la autora tiene la intención de realizarlos en la siguiente `Tarea_03`, por lo que se invita a revisarlo en caso de querer mayor información.
