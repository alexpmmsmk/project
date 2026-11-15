# Análisis y estimación de ventas de videojuegos

**Autor:** Alex Porras Martinez  
**Primera entrega:** 18/10/2026  
**Revisión:** 15/11/2026

## 1. Idea del proyecto

Para este proyecto he elegido analizar las ventas de videojuegos según su género, plataforma y región. La idea es utilizar esos datos para ayudar a una empresa a decidir qué propuestas de videojuegos le interesa estudiar y en qué plataformas puede tener más sentido publicarlas.

Además de comparar las ventas de juegos ya publicados, el proyecto se centra en una pregunta concreta: **¿cuántas copias podría vender aproximadamente un videojuego según sus características?** La respuesta será una estimación de ventas mundiales, no una garantía. Así, una empresa podrá comparar varias propuestas y ordenar las que tengan mejores previsiones, en lugar de limitarse a decir si un juego supera o no una cantidad fija de ventas.

## 2. Base de datos elegida

He elegido **Video Game Sales Dataset Updated - Extra Feat**, disponible en Kaggle. El archivo utilizado es `Video_Games.csv`. Cada fila corresponde a un videojuego en una plataforma concreta; por eso, un mismo título puede aparecer varias veces si se publicó para distintas consolas.

### Ficha de la base de datos

| Dato | Información |
|---|---|
| Nombre | Video Game Sales Dataset Updated - Extra Feat |
| Publicado por y enlace | Ibrahim Muhammad Naeem, en [Kaggle](https://www.kaggle.com/datasets/ibriiee/video-games-sales-dataset-2022-updated-extra-feat) |
| Licencia | CC0 1.0 (dominio público), indicada en Kaggle. [Consultar licencia](https://creativecommons.org/publicdomain/zero/1.0/) |
| Descarga y versión | Descargado el 18/10/2026; el archivo no incluye un número de versión |
| Qué representa cada fila | Un videojuego publicado en una plataforma determinada |
| Periodo | Años de lanzamiento de 1980 a 2020; la mayoría de los registros son de 2016 o anteriores |
| Tamaño | 16.719 filas, 16 columnas y aproximadamente 1,62 MB en CSV |
| Columna temporal | `Year_of_Release`: año en que se lanzó cada juego, no fecha de venta |
| Columnas para comparar | `Genre` (género), `Platform` (plataforma), `Publisher` (distribuidora), `Developer` (desarrolladora) y `Rating` (clasificación por edades) |
| Datos numéricos | `Global_Sales`, `NA_Sales`, `EU_Sales`, `JP_Sales`, `Other_Sales` y puntuaciones de usuarios y críticos |
| Dato que quiero estimar | `Global_Sales`: ventas mundiales de cada juego, expresadas en **millones de copias** |
| Problemas encontrados | Hay 269 videojuegos sin año de lanzamiento, 8.582 sin puntuación de críticos y 6.704 sin puntuación de usuarios. En otros 2.425 registros aparece `tbd` en la puntuación de usuarios, es decir, pendiente de determinar |
| Otras fuentes | No se necesita unir otra base de datos en esta propuesta |

El CSV se abre con pandas y permite comparar géneros, plataformas, ventas y años de lanzamiento. Solo incluye el año de publicación, no el mes de cada venta.

Los registros incompletos se revisarán antes de hacer comparaciones. Las ventas mundiales y regionales se expresan en **millones de copias, no en euros**.

## 3. Problema empresarial

### 3.1 Empresa, departamento y responsable

El caso se plantea para **PixelBridge Games**, una empresa ficticia que publica y distribuye videojuegos. Compara distintas propuestas antes de decidir en cuáles invertir.

El departamento que utilizaría los resultados es el de **estudios de mercado**. Su función consiste en analizar géneros, plataformas y resultados comerciales de otros videojuegos. El **responsable de publicaciones** consultaría el panel cada vez que tuviera que valorar un nuevo lanzamiento. Puede decidir qué propuestas tienen prioridad y en qué plataformas conviene centrarse.

### 3.2 Decisión que quiero facilitar

**Con este análisis, el responsable podrá comparar las ventas estimadas de varias propuestas y decidir cuáles merece la pena estudiar primero, teniendo en cuenta el género, la plataforma y el mercado.**

La empresa tiene un presupuesto limitado y no dispone en esta base de datos de los costes de desarrollo, publicidad ni distribución. Por tanto, las cifras estimadas servirán para comparar potencial de ventas, pero no para calcular directamente el beneficio económico de un juego. También hay que recordar que las ventas históricas son una referencia y que cada nuevo lanzamiento puede obtener resultados distintos.

### 3.3 Hipótesis que voy a comprobar

**H1. Algunos géneros, como los juegos de disparos (*Shooter*), suelen vender más copias por título que la media general.**

- **Qué voy a medir:** ventas mundiales habituales por juego y género, utilizando la mediana (el valor que queda en medio al ordenar las ventas).
- **Qué espero encontrar:** que *Shooter* tenga una cifra superior a la mediana general.
- **Cómo lo comprobaré:** con un gráfico de barras por género, mostrando también cuántos juegos hay en cada uno.
- **Si se cumple:** PixelBridge Games podrá dar más prioridad a estudiar propuestas de esos géneros.
- **Si no se cumple:** no tendrá sentido priorizarlos únicamente por su género.

**H2. Los juegos de rol tienen mayor peso en las ventas de Japón que otros géneros.**

- **Qué voy a medir:** el porcentaje de ventas de cada género que corresponde a Japón: ventas de Japón / ventas mundiales × 100.
- **Qué espero encontrar:** un porcentaje superior a la media en los juegos de rol.
- **Cómo lo comprobaré:** comparando los porcentajes de ventas de Japón con un gráfico de barras por género.
- **Si se cumple:** la empresa podrá prestar más atención a Japón al estudiar propuestas de rol.
- **Si no se cumple:** tendrá que comparar otros mercados sin dar prioridad a Japón por defecto.

**H3. Un mismo género puede conseguir ventas diferentes según la plataforma en la que se publique.**

- **Qué voy a medir:** mediana de ventas mundiales por juego, combinando género y plataforma.
- **Qué espero encontrar:** diferencias entre plataformas incluso dentro de un mismo género.
- **Cómo lo comprobaré:** con una tabla y gráficos filtrados por género, plataforma y año de lanzamiento.
- **Si se cumple:** la empresa podrá comparar qué combinaciones de género y consola tienen mejores resultados.
- **Si no se cumple:** tendrá que dar más importancia a otros factores antes de elegir plataforma.

Estas hipótesis sirven para comprobar relaciones en los datos. No se consideran demostradas hasta realizar las comparaciones.

### 3.4 Indicadores del panel (KPIs)

| Indicador | Cómo se calcula | Qué se compara | Referencia |
|---|---|---|---|
| Ventas mundiales totales | Suma de `Global_Sales`, en millones de copias | Género, plataforma y año de lanzamiento | Ventas totales de los grupos seleccionados |
| Ventas medias por juego | Suma de ventas mundiales / número de registros del grupo | Género y plataforma | Media general de la base de datos |
| Ventas habituales por juego | Mediana de `Global_Sales` de cada grupo | Género, plataforma y año | Mediana general de la base de datos; relacionada con H1 y H3 |
| Peso de Japón en las ventas | Suma de `JP_Sales` / suma de `Global_Sales` × 100 | Género y año | Porcentaje general de Japón; relacionado con H2 |
| Ventas estimadas por propuesta | Resultado del modelo, en millones de copias | Propuesta, género y plataforma | Ordenar las propuestas de mayor a menor venta prevista |

Para interpretar bien estos indicadores también se mostrará el número de videojuegos de cada grupo, ya que no sería justo comparar una plataforma con cientos de juegos con otra que apenas tenga unos pocos.

## 4. Diseño de la solución

### 4.1 Qué solución se plantea

La solución principal es un **dashboard**, un panel con gráficos e indicadores que permite comparar géneros, plataformas, años de lanzamiento y regiones. Se complementa con un **modelo de estimación de ventas** que proporciona una cifra aproximada de copias mundiales para cada propuesta y permite ordenarlas de mayor a menor venta prevista.

La información de entrada procede del CSV de Kaggle. Se carga con **Python y pandas**, se revisan los datos vacíos y se organiza una tabla para el análisis. El modelo utilizará características que pueden conocerse al preparar un lanzamiento, como el género, la plataforma, la distribuidora o el año previsto. El resultado será una estimación en millones de copias y una comparación de alternativas para el responsable de publicaciones.

**Las ventas regionales no se utilizarán para calcular las ventas mundiales previstas**, porque sus cifras ya forman parte del total mundial y estaríamos dando al modelo casi toda la respuesta. Las puntuaciones de usuarios y críticos tampoco se utilizarán como información previa al lanzamiento, porque normalmente se conocen después de publicar el juego. La precisión de las estimaciones se comprobará comparándolas con ventas reales de registros reservados para la evaluación.

### 4.2 Diagrama de arquitectura

```mermaid
flowchart LR
    A[Origen: CSV de Kaggle] --> B[Carga: Python y pandas]
    B --> C[Limpieza: vacíos y tipos de datos]
    C --> D[Tabla preparada para analizar]
    D --> E[Indicadores y estimación de ventas]
    E --> F[Dashboard con Streamlit]
    F --> G[Responsable toma decisiones]
```

| Parte | Qué hace | Herramienta | Cuándo se actualiza |
|---|---|---|---|
| Origen | Contiene los registros originales de videojuegos | Kaggle, CSV | Al descargar otra versión de los datos |
| Carga | Abre el archivo y lee las columnas | Python y pandas | Cada vez que se importe el CSV |
| Limpieza | Revisa valores vacíos, `tbd` y tipos de datos | pandas | Después de cada carga |
| Tabla preparada | Organiza las columnas necesarias para el análisis y la estimación | CSV o Parquet | Después de limpiar los datos |
| Análisis y estimación | Calcula indicadores y estima ventas mundiales aproximadas | pandas y scikit-learn | Al ejecutar el análisis o cambiar el modelo |
| Panel de resultados | Presenta gráficos, filtros y comparaciones | Streamlit | Cuando se actualicen los resultados |

El responsable de actualizar los archivos y volver a ejecutar el análisis seré yo. Como el conjunto contiene datos históricos, no se plantea una actualización diaria. El código se guardará en la carpeta `project/` de GitHub y se mantendrá el enlace al archivo original de Kaggle.

Si cambia el formato del CSV, comprobaré los nombres y tipos de columnas antes de cargarlo de nuevo. Los principales riesgos son la información incompleta, las diferencias grandes entre los juegos más vendidos y los demás, y el uso accidental de columnas que ya contienen las ventas que queremos estimar. Por eso, el panel mostrará las estimaciones como orientativas y no como ventas garantizadas. El archivo no incluye datos personales identificativos de clientes, aunque igualmente se respetará su licencia de uso.

## 5. Fuentes

Naeem, I. M. (s. f.) *Video Game Sales Dataset Updated - Extra Feat*. Kaggle. Disponible en: https://www.kaggle.com/datasets/ibriiee/video-games-sales-dataset-2022-updated-extra-feat (consulta: 18 de octubre de 2026).

Creative Commons (s. f.) *CC0 1.0 Universal — Public Domain Dedication*. Disponible en: https://creativecommons.org/publicdomain/zero/1.0/ (consulta: 18 de octubre de 2026).

OpenAI (2026). *ChatGPT*. Herramienta de apoyo para consultar información y revisar la claridad de la redacción. Disponible en: https://chatgpt.com/ (consulta: 15 de noviembre de 2026).
