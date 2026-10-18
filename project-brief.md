# Análisis de ventas de videojuegos

**Autor:** Alex Porras Martinez  
**Fecha:** 18 de octubre de 2026

## 1. Idea del proyecto

Para este proyecto he elegido una base de datos de ventas de videojuegos. Mi idea es analizar qué géneros, consolas y regiones han tenido mejores resultados y ver cómo esa información podría ayudar a una empresa a tomar decisiones.

Por ejemplo, una empresa que publica videojuegos tiene que decidir en qué tipos de juegos invertir y para qué plataformas sacarlos. En lugar de basarse solo en opiniones, podría mirar qué ha pasado con otros videojuegos parecidos. El objetivo es contar con información útil para comparar géneros y plataformas y valorar qué videojuegos podrían tener mejores resultados comerciales.

El estudio utiliza información de videojuegos de diferentes años para identificar tendencias y apoyar decisiones sobre nuevos lanzamientos.

## 2. Base de datos elegida

He escogido **Video Game Sales Dataset Updated - Extra Feat**, que está disponible en Kaggle. He descargado el archivo `Video_Games.csv` y he revisado sus datos con Python. Cada fila representa un videojuego en una plataforma concreta. Por eso, un mismo título puede aparecer varias veces si salió para distintas consolas.

### Ficha de la base de datos

| Dato | Información |
|---|---|
| Nombre | Video Game Sales Dataset Updated - Extra Feat |
| Publicado por y enlace | Ibrahim Muhammad Naeem, en [Kaggle](https://www.kaggle.com/datasets/ibriiee/video-games-sales-dataset-2022-updated-extra-feat) |
| Licencia | CC0 (dominio público), según la ficha de Kaggle. [Información de la licencia](https://creativecommons.org/publicdomain/zero/1.0/) |
| Fecha de descarga y versión | 18/10/2026. Versión descargada desde la página de Kaggle; el ZIP no indica un número de versión |
| Qué representa una fila | Un videojuego publicado para una plataforma concreta |
| Periodo | Videojuegos lanzados entre 1980 y 2020 |
| Tamaño | 16.719 filas, 16 columnas y un CSV de aproximadamente 1,62 MB |
| Año de lanzamiento | `Year_of_Release`: indica el año en que se publicó el videojuego, no la fecha de sus ventas |
| Categorías | `Platform`, `Genre`, `Publisher`, `Developer`, `Rating` |
| Datos numéricos | `Global_Sales`, `EU_Sales`, `NA_Sales`, `JP_Sales`, `Other_Sales` y puntuaciones |
| Resultado que se quiere estimar | Si un videojuego puede alcanzar **1 millón de copias vendidas** en todo el mundo |
| Problemas encontrados | En 269 videojuegos no aparece el año de lanzamiento; 8.582 videojuegos no tienen puntuación de críticos y 6.704 no tienen puntuación de usuarios. Además, en 2.425 registros figura `tbd`, que significa que la puntuación todavía no estaba determinada. |
| Otras fuentes | De momento no necesito unir otra base de datos |

Esta base de datos cumple el mínimo de 5.000 filas, tiene varias categorías, varias columnas numéricas y años suficientes para comparar diferentes periodos. Además, está en formato CSV, así que puedo abrirla y analizarla con pandas.

El año de lanzamiento permite comparar los videojuegos publicados en diferentes épocas. No indica el mes en el que se vendieron las copias, por lo que las comparaciones se realizarán por año de lanzamiento. También hay juegos sin fecha o puntuaciones disponibles; esos datos se tendrán en cuenta para que las comparaciones sean correctas.

## 3. Problema que quiero resolver

### 3.1 Empresa, departamento y persona que usaría los resultados

Para el caso voy a utilizar una empresa ficticia llamada **PixelBridge Games**, dedicada a publicar y distribuir videojuegos. Antes de sacar un juego al mercado, tiene que estudiar si vale la pena invertir en él y en qué plataformas podría funcionar mejor.

El departamento que utilizaría el proyecto sería el de **estudios de mercado**. Su trabajo es comparar juegos, géneros y ventas para ayudar a planificar nuevos lanzamientos.

La persona encargada de consultar los resultados sería el **responsable de publicaciones**. Los utilizaría al valorar nuevos lanzamientos para comparar géneros, plataformas y mercados antes de tomar decisiones.

### 3.2 Decisión que quiero facilitar

**Con este proyecto quiero ayudar al responsable a decidir qué combinaciones de género, plataforma y mercado debería estudiar antes de invertir en un videojuego.**

La empresa cuenta con un presupuesto limitado y necesita elegir bien dónde invertir. Este análisis ayuda a comparar oportunidades a partir de las ventas, aunque los datos no incluyen los costes de desarrollo ni de publicidad. Por eso el resultado sirve como apoyo para elegir proyectos, no como cálculo de beneficios.

### 3.3 Hipótesis que voy a comprobar

**H1. Algunos géneros tienen más posibilidades de superar el millón de copias que otros.**

- **Qué voy a medir:** porcentaje de juegos que llegan a 1 millón de copias dentro de cada género.
- **Qué espero encontrar:** que géneros como *Shooter* o *Platform* tengan un porcentaje superior a la media.
- **Cómo lo comprobaré:** con un gráfico de barras por género, indicando también cuántos juegos hay en cada grupo.
- **Si se cumple:** la empresa podría estudiar esos géneros con más interés.
- **Si no se cumple:** no tendría sentido darles prioridad solo por su género.

**H2. Los juegos de rol tienen más peso en las ventas de Japón que otros géneros.**

- **Qué voy a medir:** porcentaje de ventas que corresponden a Japón (`JP_Sales / Global_Sales × 100`), calculado con las ventas totales de cada género.
- **Qué espero encontrar:** que los juegos de rol tengan un porcentaje de ventas japonesas superior a la media.
- **Cómo lo comprobaré:** comparando las ventas por región y género con gráficos de barras.
- **Si se cumple:** la empresa podría estudiar Japón como mercado para ese tipo de juegos.
- **Si no se cumple:** tendría que valorar otras regiones sin dar por hecho que Japón es la mejor opción.

**H3. La plataforma influye en los resultados de ventas de un mismo género.**

- **Qué voy a medir:** porcentaje de juegos que superan el millón de copias según plataforma y género.
- **Qué espero encontrar:** diferencias entre plataformas, incluso al comparar juegos de un género parecido y años similares.
- **Cómo lo comprobaré:** con una tabla o gráfico que permita filtrar por género, plataforma y año.
- **Si se cumple:** la empresa podría investigar las combinaciones que tengan mejores resultados.
- **Si no se cumple:** tendría que fijarse más en otros factores, no solo en la consola.

Son tres hipótesis que se contrastan con los datos, sin dar por hecho su resultado.

### 3.4 Indicadores que tendrá el panel (KPIs)

| Indicador | Cómo lo calcularé | Comparación | Referencia |
|---|---|---|---|
| Ventas mundiales totales | Suma de `Global_Sales`, en millones de copias | Género, plataforma y año | Comparar grupos entre sí |
| Media de ventas por juego | Ventas mundiales totales / número de registros | Género y plataforma | Comparar con la media general |
| Porcentaje de juegos de éxito | Juegos con al menos 1 millón de copias / total de juegos × 100 | Género, plataforma y periodo | Media del dataset: aproximadamente 12,4 % |
| Peso de Japón en las ventas | Ventas de Japón / ventas mundiales × 100 | Género y año | Comparar con el porcentaje general |

El porcentaje de juegos de éxito me servirá para comprobar las hipótesis H1 y H3. El peso de Japón me ayudará con H2. También miraré el número de juegos de cada grupo para no sacar conclusiones de categorías que solo tengan unos pocos registros.

## 4. Diseño de la solución

### 4.1 Tipo de solución

La solución consiste en un **dashboard** (panel de información) con cifras de ventas, gráficos y filtros por género, plataforma y año de lanzamiento. También incluye un análisis para estimar si un videojuego puede alcanzar el millón de copias vendidas, a partir de características como su género, plataforma y año.

Los datos proceden del archivo CSV de Kaggle. Se organizan y revisan con **Python y pandas**, se calculan los indicadores y se presentan los resultados en el panel. Para estimar el éxito comercial no se utilizan como datos de entrada las propias ventas regionales, porque su suma ya permite conocer las ventas mundiales. La empresa utiliza esta información para comparar opciones antes de decidir qué videojuegos publicar.

### 4.2 Diagrama de arquitectura

```mermaid
flowchart LR
    A[Origen: CSV de Kaggle] --> B[Carga: Python y pandas]
    B --> C[Limpieza: vacíos y tipos de datos]
    C --> D[Tabla preparada para analizar]
    D --> E[Indicadores y modelo predictivo]
    E --> F[Dashboard con Streamlit]
    F --> G[Responsable toma decisiones]
```

| Parte | Qué hará | Herramienta | Actualización |
|---|---|---|---|
| Origen | Guardar la información original de videojuegos | Kaggle, CSV | Datos históricos |
| Carga | Abrir el archivo y leer sus columnas | Python y pandas | Cuando vuelva a importar el archivo |
| Limpieza | Revisar vacíos, `tbd` y tipos de datos | pandas | Cada vez que cargue datos nuevos |
| Tabla preparada | Dejar los registros listos y añadir la columna de éxito | CSV o Parquet | Después de limpiar |
| Análisis | Calcular indicadores y estimar el éxito comercial | pandas y scikit-learn | Cuando ejecute el análisis |
| Dashboard | Mostrar resultados con gráficos y filtros | Streamlit | Cuando cambien los datos preparados |

Al tratarse de una base de datos histórica, **no hace falta actualizarla todos los días**. Yo me encargaré de volver a ejecutar el proceso cuando sea necesario. El código estará en la carpeta `project/` de mi repositorio de GitHub y pondré el enlace a Kaggle para identificar la fuente original.

Si algún día cambia el nombre o el formato de las columnas del archivo, primero comprobaré el error y adaptaré la carga antes de seguir. Los principales riesgos son que haya información incompleta, que algunas plataformas tengan pocos videojuegos y que se utilicen en una predicción cifras de ventas que ya revelan el resultado. Estas cuestiones se tendrán en cuenta al interpretar los datos. Los datos hablan de juegos y empresas, no de clientes identificados personalmente, por lo que el riesgo de privacidad es más bajo, aunque igualmente hay que respetar la licencia de uso.

## 5. Fuentes

Naeem, I. M. (s. f.) *Video Game Sales Dataset Updated - Extra Feat*. Kaggle. Disponible en: https://www.kaggle.com/datasets/ibriiee/video-games-sales-dataset-2022-updated-extra-feat (Consulta: 18 de octubre de 2026).

Creative Commons (s. f.) *CC0 1.0 Universal — Public Domain Dedication*. Disponible en: https://creativecommons.org/publicdomain/zero/1.0/ (Consulta: 18 de octubre de 2026).

OpenAI (2026). *ChatGPT*. Herramienta de apoyo para consultar información y revisar la claridad de la redacción. Disponible en: https://chatgpt.com/ (Consulta: 18 de octubre de 2026).
