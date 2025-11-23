# 📖 La Biblia de Power Query

> Esta herramienta es tu "ETL" (Extract, Transform, Load) personal. Y lo más importante: **es un lenguaje visual con un motor de código (M) por debajo.**
> **Nota:** Bienvenido al taller de alquimia. Si SQL es como *extraer* el mineral de la montaña, Power Query es donde *refinas* ese mineral, eliminas las impurezas y lo conviertes en oro puro (datos limpios y listos para el modelo).
> **Nota 2:** Una vez que tus ingredientes (tablas) están limpios, es hora de cocinarlos y ensamblar el plato. A partir de la **sección 5**, verás sobre cómo **combinar** y **resumir** datos. Aquí es donde aplicamos directamente los conceptos de `JOIN` y `GROUP BY` de SQL, pero con una interfaz visual mucho más intuitiva.
> **Nota 3:** A partir de la **sección 10** aprenderás a ser el "arquitecto" del taller. No se trata de *transformar* datos, sino de *gestionar el flujo* de tus consultas. Esto es lo que separa un modelo de Power BI robusto y rápido de uno lento y frágil.

---

## 1. ¿Qué es Power Query?

Power Query (PQ) es un motor de transformación y preparación de datos. Existe dentro de Excel, Power BI, Azure Data Factory (como "Wrangling Data Flows") y Fabric.

**Analogía Central: La Cocina de Datos:**

* **Ingredientes Crudos (`Extract`):** Tus orígenes (un Excel sucio, una tabla SQL, un JSON de una web).
* **La Receta (`Transform`):** La lista de pasos que aplicas (quitar columnas, filtrar filas, pivotar).
* **El Plato Final (`Load`):** Una tabla limpia y perfecta, lista para ser servida en Power BI o Excel.

La magia de PQ es que **graba la receta (los "Pasos Aplicados"), no el resultado**. Cuando le das "Refrescar", simplemente vuelve a ejecutar la receta completa con los ingredientes nuevos.

---

## 2. Los 4 Conceptos Clave

### 2.1. Inmutabilidad (No se tocan los originales)

Power Query **NUNCA** modifica tus archivos de origen. Es de solo lectura.

* **Analogía:** Es como un chef que lee una receta de un libro antiguo. Hace una *copia* de la receta en su libreta y trabaja sobre esa copia. El libro original (tu Excel, tu CSV) permanece intacto.

### 2.2. Pasos Aplicados (Applied Steps)

Cada clic que haces en la interfaz (filtrar, renombrar columna, etc.) se graba como un paso en el panel derecho.

* Esto es tu "Control-Z" infinito. Puedes hacer clic en un paso anterior para ver cómo estaban los datos en ese momento.
* Puedes reordenar, modificar o eliminar pasos (¡con cuidado!).
* Cada paso es, en realidad, una línea de código **M** que la interfaz escribe por ti.

### 2.3. Evaluación Perezosa (Lazy Evaluation)

Power Query no ejecuta la transformación *completa* de 1 millón de filas cada vez que haces un clic. Solo te muestra una vista previa (usualmente las primeras 1000 filas) para que veas el efecto de tu transformación. La "magia" real solo ocurre cuando le das a "Cerrar y Aplicar" o "Refrescar".

### 2.4. Plegado de Consultas (Query Folding)

¡Este es el concepto más importante para tu rendimiento con SQL!

* **¿Qué es?** Es la habilidad de Power Query para traducir tus clics (tus transformaciones) de vuelta al lenguaje del origen, **principalmente SQL**.
* **Analogía:** Eres el jefe y le pides a tu asistente (Power Query) que te traiga "solo los cafés vendidos en España el último mes".
  * **Sin Folding (Ineficiente):** El asistente va al almacén (SQL Server), se trae **TODOS** los cafés de **TODOS** los países de **TODA** la historia, los pone en tu escritorio y *luego* empieza a filtrar.
  * **Con Folding (Eficiente):** El asistente (Power Query) piensa y le grita al almacenista (SQL Server): "¡Oye, prepárame una caja *solo* con los cafés de España del último mes!". El almacenista (SQL) hace el trabajo pesado y tu asistente solo te trae la caja pequeña que pediste.

**¿Cómo saber si está funcionando?**
En el panel de "Pasos Aplicados", haz clic derecho en un paso. Si la opción `"Ver Consulta Nativa"` está habilitada, ¡felicidades, el Query Folding está activo! Si está gris, ese paso (y todos los siguientes) se están procesando en tu máquina local, no en el servidor.

* **Pasos que "pliegan" (Folding):** Filtrar, Ordenar, Renombrar, Ciertas matemáticas, Agrupar, Unir (`Merge`).
* **Pasos que "rompen" el Folding:** Usar funciones M personalizadas, "Rellenar hacia abajo", "Dinamizar/Anular dinamización" (Pivot/Unpivot), Añadir columna de índice.

---

## 3. La Interfaz (El Taller)

1. **La Cinta (Ribbon):** Como en Office. Aquí están todas las transformaciones predefinidas.
    * **`Transformar`:** Modifica la columna *actual* (ej. cambiar tipo de dato).
    * **`Agregar Columna`:** Crea una *nueva* columna (ej. columna condicional).
2. **Lista de Consultas (Queries):** A la izquierda. Todas tus "recetas".
3. **Vista Previa de Datos:** El centro. Donde ves el resultado de tus pasos.
4. **Configuración de Consulta (Panel Derecho):**
    * **`Nombre`:** ¡Ponle nombres decentes a tus consultas! (ej. `dim_Clientes` en lugar de `Tabla1`).
    * **`Pasos Aplicados`:** El historial de tu receta. ¡El corazón de PQ!

---

## 4. Transformaciones Básicas (Las Herramientas)

Estas son las acciones del "clic derecho" o de la cinta que usarás el 90% del tiempo.

### 4.1. Elegir y Quitar Columnas

* **`Elegir Columnas`:** Selecciona solo las que necesitas.
  * **Mejor Práctica:** Siempre es mejor "Elegir" las 5 columnas que quieres que "Quitar" las 50 que no quieres. Si en el futuro se añade una columna nueva en el origen (`columna_56`), tu query no se romperá ni la cargará innecesariamente.
* **`Quitar Columnas`:** Elimina las seleccionadas.

### 4.2. Filtrar Filas

Igual que en Excel. Usa los menús desplegables de cada columna para filtrar.

* **`Quitar Vacíos` (Remove Empty):** Tu mejor amigo para limpiar datos.
* **`Filtros de Texto`:** Contiene, Empieza con, No contiene...
* **`Filtros de Número/Fecha`:** Mayor que, Menor que, En el mes anterior...

### 4.3. Cambiar Tipo de Dato

PQ intenta adivinar el tipo de dato, pero a menudo falla. Es **tu responsabilidad** asegurarte de que los números sean `Decimal` o `Entero`, las fechas sean `Fecha` y el texto sea `Texto`.

* **¡Importante!** Haz esto al principio de tus pasos (si los datos ya están limpios) o al final (después de limpiar valores no numéricos, etc.). Un tipo de dato incorrecto puede arruinar tus cálculos en Power BI.

### 4.4. Reemplazar Valores

Clic derecho en un valor o en la columna -> `Reemplazar los valores`.

* **Caso de uso:** Limpiar datos sucios. Reemplazar "N/A", "nulo", "sin dato" por `null`. (OJO: `null` (de PQ) es diferente a un texto `"null"`).

### 4.5. Dividir Columnas

`Transformar` -> `Dividir Columna`.

* **Por delimitador:** Ideal para CSVs o campos como "Nombre Apellido" (dividir por espacio).
* **Por número de caracteres:** Para códigos fijos como "SKU12345".

### 4.6. Combinar Columnas

Selecciona dos o más columnas -> `Agregar Columna` -> `Combinar Columnas`.

* **Caso de uso:** Crear claves únicas. (Ej. `PedidoID` y `LineaPedidoID` para crear `ClaveVentaUnica`).

---

## 5. Combinar Consultas (`Merge`): El "JOIN" de PQ

`Merge` te permite unir dos tablas (consultas) horizontalmente, basándose en una o más columnas coincidentes. Es el equivalente exacto a un `JOIN` en SQL.

**Analogía:** Tienes dos listas. Una con `ID_Cliente` y `Nombre`. Otra con `ID_Cliente` y `Ventas`. `Merge` te permite usar el `ID_Cliente` (la "llave") para poner el `Nombre` correcto al lado de la `Venta` correcta.

### 5.1. Cómo Usarlo

1. Selecciona tu consulta "izquierda" (la principal, ej. `Fact_Ventas`).
2. Ve a la pestaña `Inicio` -> `Combinar Consultas` (`Merge Queries`).
3. Selecciona la tabla "derecha" (la que quieres unir, ej. `dim_Clientes`).
4. Haz clic en la(s) columna(s) "llave" en ambas tablas (ej. `Cliente_ID`).
5. **¡Importante!** Selecciona el `Tipo de combinación` (`Join Kind`).

### 5.2. Tipos de Combinación (Tipos de JOIN)

Esto es idéntico a SQL, pero con nombres más amigables:

* **`Externa izquierda` (Left Outer):** (La más común, 95% de las veces).
  * Todas las filas de la primera tabla (`Fact_Ventas`), y solo las coincidencias de la segunda (`dim_Clientes`).
  * *Uso:* "Quiero todas mis ventas, y si el cliente existe, tráeme su nombre".

* **`Externa derecha` (Right Outer):**
  * Todas las filas de la segunda, solo coincidencias de la primera. (Rara vez se usa, es mejor invertir el orden y usar `Left Outer`).

* **`Interna` (Inner):**
  * Solo las filas que coinciden en **ambas** tablas.
  * *Uso:* "Quiero ver ventas *solamente* de clientes que SÍ tengo en mi maestro de clientes".

* **`Completa externa` (Full Outer):**
  * Todas las filas de ambas tablas, coincidan o no.

* **`Anti izquierda` (Left Anti):**
  * Solo las filas de la primera tabla que **NO** tienen coincidencia en la segunda.
  * *Uso (¡muy útil!):* "Quiero encontrar todas las Ventas que tienen un `Cliente_ID` *huérfano* (que no existe en mi tabla `dim_Clientes`)".

* **`Anti derecha` (Right Anti):**
  * Solo las filas de la segunda que no tienen coincidencia en la primera.
  * *Uso:* "Quiero encontrar todos los Clientes que *nunca* han comprado".

### 5.3. El Paso "Expandir"

Después de hacer `Merge`, PQ no te muestra las columnas de inmediato. Te da una nueva columna de tipo `[Table]`. Debes hacer clic en el icono de "Expandir" (dos flechas) en el encabezado de esa columna.

* **Mejor Práctica:** Al expandir, **desmarca siempre** la casilla `"Usar el nombre de columna original como prefijo"`. Si no lo haces, tus columnas se llamarán `dim_Clientes.NombreCliente` en lugar de solo `NombreCliente`.
* Solo expande las columnas que realmente necesitas. No traigas el `Cliente_ID` de nuevo si ya lo tienes.

---

## 6. Anexar Consultas (`Append`): El "UNION ALL" de PQ

`Append` te permite apilar dos o más tablas (consultas) verticalmente. Es el equivalente exacto a un `UNION ALL` en SQL.

**Analogía:** Tienes una hoja de cálculo con las ventas de "Enero" y otra hoja con las ventas de "Febrero". `Append` las pega una debajo de la otra para crear una sola tabla de "Ventas Totales".

* **Requisito:** Las tablas deben tener, idealmente, los mismos nombres de columna y tipos de dato.
* Si una tabla tiene una columna que la otra no, `Append` funcionará, pero rellenará las filas faltantes con `null`.
* **Uso:** Consolidar datos de diferentes períodos (ventas 2023, ventas 2024), diferentes regiones (ventas USA, ventas Europa) o diferentes sistemas.

---

## 7. Agrupar Por (`Group By`): El "GROUP BY" de PQ

Resume (agrega) una tabla basándose en una o más columnas. Es el equivalente a `GROUP BY` en SQL.

**Analogía:** Tienes una lista de 1000 ventas individuales. `Group By` la convierte en una lista de 10 países, con el `SUM(Ventas)` al lado de cada uno.

* **Ubicación:** Pestaña `Transformar` -> `Agrupar por`.
* **Uso Básico:**
  * `Agrupar por:` `País`
  * `Nuevo nombre de columna:` `Ventas Totales`
  * `Operación:` `Suma`
  * `Columna:` `MontoVenta`
* **Uso Avanzado:** Puedes agrupar por múltiples columnas y crear múltiples agregaciones (`Suma`, `Recuento`, `Promedio`, `Min`, `Max`, etc.).

**¡Importante!** `Group By` **destruye** el nivel de detalle original. Estás colapsando 1000 filas en 10. Úsalo con intención, por ejemplo, para crear una tabla agregada.

---

## 8. Pivotar y Anular Dinamización (¡La Magia de PQ!)

Aquí es donde Power Query brilla y deja a SQL muy atrás en facilidad de uso.

### 8.1. Anular Dinamización de Columnas (`Unpivot`)

Esta es **la transformación más importante** para limpiar datos que vienen de Excel. Transforma datos "anchos" (malos para análisis) en datos "largos" (buenos para análisis).

**Analogía:** Tienes una tabla de Excel donde los meses son columnas (Ene, Feb, Mar...). `Unpivot` "derrite" esas columnas en dos nuevas: una llamada `Atributo` (que contendrá "Ene", "Feb", "Mar") y otra llamada `Valor` (que contendrá las ventas).

* **Datos "Anchos" (Malos):**
| Producto | Ene | Feb | Mar |
| :--- | :-: | :-: | :-: |
| Leche | 100 | 120 | 110 |
| Pan | 80 | 85 | 90 |

* **Cómo:** Selecciona la(s) columna(s) que NO quieres "derretir" (en este caso, `Producto`). Haz clic derecho -> `Anular dinamización de OTRAS columnas`.

* **Datos "Largos" (Buenos):**
| Producto | Atributo | Valor |
| :--- | :--- | ----: |
| Leche | Ene | 100 |
| Leche | Feb | 120 |
| Leche | Mar | 110 |
| Pan | Ene | 80 |
| Pan | Feb | 85 |
| Pan | Mar | 90 |

### 8.2. Columna Dinámica (`Pivot`)

Es la operación opuesta. Transforma datos "largos" en "anchos". Es el `PIVOT` de SQL.

* **Cómo:**
    1. Selecciona la columna que contiene los nombres que quieres convertir en **nuevos encabezados** (ej. la columna `Atributo` de arriba).
    2. Ve a `Transformar` -> `Columna dinámica`.
    3. Te preguntará: `Columna de valores:` (ej. `Valor`).
    4. En `Opciones avanzadas`, selecciona la `Función de valor de agregado:` (ej. `Suma` o `No agregar` si los valores son únicos).

---

## 9. Columna Condicional (`CASE WHEN`)

Es la forma visual de crear una sentencia `CASE WHEN` de SQL o un `SI()` (IF) de Excel.

**Analogía:** Es el "Sombrero Seleccionador" que crea una nueva columna (ej. "Grupo de Edad") basado en el valor de otra (ej. "Edad").

* **Ubicación:** Pestaña `Agregar columna` -> `Columna condicional`.
* **Interfaz:** Rellenas los campos de forma muy intuitiva:
  * `Si` [columna] `es mayor que` [valor] `entonces` [salida]
  * `Si no, si` [columna] `es igual a` [valor] `entonces` [salida]
  * `Si no` [salida por defecto]

**Ejemplo:**

* Si `Ventas` > 1000 ENTONCES `Alto`
* Si no, si `Ventas` > 500 ENTONCES `Medio`
* Si no `Bajo`

---

## 10. Referenciar vs. Duplicar: ¡El Concepto Arquitectónico Clave

Cuando haces clic derecho en una consulta, tienes dos opciones: `Duplicar` y `Referenciar`. Entender la diferencia es **fundamental**.

**Analogía:** Piensa en un archivo en tu ordenador.

* `Duplicar` es **"Copiar y Pegar"** (`Ctrl+C`, `Ctrl+V`). Creas un archivo completamente nuevo e independiente. Si cambias el original, la copia no se entera.
* `Referenciar` es **"Crear un Acceso Directo"**. Creas un "puntero" que *apunta* al original. El acceso directo no contiene datos, solo es una ruta. Si el original se actualiza, el acceso directo (la referencia) muestra esa actualización.

### `Duplicar` (Duplicate)

* **Qué hace:** Crea una copia **idéntica e independiente** de la consulta, incluyendo todos sus "Pasos Aplicados".
* **Cuándo usarlo:**
  * Para hacer pruebas o "jugar" (`what-if`) sin arruinar tu consulta original.
  * Cuando quieres crear una versión *ligeramente diferente* de una consulta, pero que seguirá un camino de transformación distinto.

### `Referenciar` (Reference)

* **Qué hace:** Crea una **nueva consulta dependiente** que usa la *salida final* de la consulta original como su *fuente*.
* **Cuándo usarlo (¡El 90% del tiempo!):**
  * **Para construir tu Esquema de Estrella.** Este es el caso de uso principal.
  * **Ejemplo:**
        1. Creas una consulta `Ventas_Base` que se conecta a SQL y hace la limpieza inicial (10 pasos).
        2. **Referencias** `Ventas_Base` para crear tu `fact_Ventas`. (Paso 1: Fuente = `Ventas_Base`, Paso 2: Elegir columnas de hechos).
        3. **Referencias** `Ventas_Base` de nuevo para crear tu `dim_Productos`. (Paso 1: Fuente = `Ventas_Base`, Paso 2: Elegir columnas de producto, Paso 3: Quitar duplicados).
        4. **Referencias** `Ventas_Base` otra vez para crear tu `dim_Clientes`.

* **Ventaja:** Power Query solo ejecuta la consulta `Ventas_Base` **una vez** y luego usa ese resultado en memoria para alimentar las otras tres tablas. Es *infinitamente* más eficiente.

> **Buena Práctica:** A menudo querrás *Deshabilitar la Carga* (clic derecho) de la consulta `Ventas_Base` para que no se cargue en el modelo de Power BI, sino que solo sirva como un "bloque de construcción" intermedio dentro de Power Query.

---

## 11. Parámetros: Haciendo tus Consultas Dinámicas

Los parámetros te permiten "externalizar" valores que usas dentro de tus consultas, como rutas de archivos, nombres de servidor o fechas de filtro.

**Analogía:** Un parámetro es como el **"Menú de Configuración"** de una aplicación. En lugar de escribir el nombre del servidor "a fuego" (hardcoding) en 10 consultas diferentes, lo pones en un parámetro `p_NombreServidor` y tus 10 consultas leen de ahí.

* **Ubicación:** Pestaña `Inicio` -> `Administrar Parámetros`.
* **Caso de uso 1: Rutas de Archivos.**
  * Creas un parámetro `p_RutaArchivoExcel` con el valor `"C:\Reportes\Ventas.xlsx"`.
  * Tu consulta `Excel.Workbook(File.Contents(p_RutaArchivoExcel), ...)` usa ese parámetro.
  * Cuando mueves el archivo, solo cambias el parámetro en un lugar, no editas la consulta.
* **Caso de uso 2: Entornos (Dev/Prod).**
  * Creas un parámetro `p_ServidorSQL` con el valor `"Servidor_Produccion"`.
  * Todas tus consultas SQL usan `Sql.Database(p_ServidorSQL, ...)`
  * Para cambiar a pruebas, solo editas el parámetro a `"Servidor_Desarrollo"`.

---

## 12. Funciones (El Puente hacia "M")

Power Query te permite convertir una consulta en una **Función** reutilizable.

**Analogía:** Has construido una "máquina" (la consulta) que limpia un tipo específico de archivo. Convertirla en una función es como **ponerle un botón de "Start"** y hacerla capaz de recibir *cualquier* archivo que le pases.

### El Caso Mágico: Combinar Archivos desde Carpeta

Este es el mejor ejemplo de funciones automáticas:

1. Te conectas a un origen de datos tipo `Carpeta` (Folder).
2. Power Query te muestra una lista de los archivos (ej. 12 archivos CSV, uno por cada mes).
3. Haces clic en el botón **`Combinar Archivos`**.
4. **Magia de PQ:** En segundo plano, PQ hace lo siguiente:
    * Toma el *primer archivo* como muestra.
    * Te pide que definas la "receta" de limpieza para *ese* archivo (ej. `Promover encabezados`, `Cambiar tipo`).
    * Convierte esa "receta" en una **Función** (ej. `fn_TransformarArchivo`).
    * Crea una consulta final que **invoca (llama) a esa función** por cada archivo en la carpeta y luego apila (`Append`) todos los resultados.

Este proceso te enseña el poder de las funciones sin que tengas que escribir una sola línea de código M.

---

## 13. Herramientas de Perfilado de Datos (El "Data Scientist")

Estas son las herramientas que te dicen la "salud" de tus datos.

* **Ubicación:** Pestaña `Vista` (View).
* **`Calidad de Columnas` (Column Quality):** Te da un resumen rápido sobre cada columna: `Válido %`, `Error %`, `Vacío %`.
* **`Distribución de Columnas` (Column Distribution):** Te muestra un histograma de los valores, cuántos son `Distintos` (Cardinalidad) y cuántos son `Únicos`.
* **`Perfil de Columna` (Column Profile):** El más detallado. Te da estadísticas completas (Min, Max, Promedio, etc.) y una visualización de la distribución de los valores.

**Analogía:** Son los **"Rayos X"** o el **"Análisis de Sangre"** de tus datos. Antes de operar (transformar), diagnosticas el problema. Si `Calidad de Columnas` te dice que tienes un 5% de errores, sabes que necesitas un paso de `Reemplazar Errores`.

---

## 14. Manejo de Errores (UI)

¿Qué pasa cuando una conversión de tipo de dato falla (ej. tratar de convertir "N/A" a número)? Se genera un `Error`.

* **`Quitar Errores` (Remove Errors):** (Clic derecho en la columna) Simplemente elimina cualquier fila que contenga un error en esa columna. Rápido pero peligroso (puedes perder datos).
* **`Reemplazar Errores` (Replace Errors):** (Clic derecho en la columna) Te permite reemplazar el `Error` por un valor específico (ej. `null` o `0`). Esta es la forma preferida y más controlada de limpiar.

---

## 15. Otras Transformaciones Útiles (La Caja de Herramientas)

* **`Rellenar` (Fill Down / Fill Up):**
  * **Caso de uso:** Datos de Excel donde una celda (ej. "Ventas") aplica a las 10 filas de abajo, que están vacías (`null`). `Rellenar Hacia Abajo` copia el valor "Ventas" en todas las celdas `null` hasta que encuentra un nuevo valor.
* **`Agregar Columna de Índice` (Add Index Column):**
  * Agrega una columna con un contador único (0, 1, 2, 3...).
  * **Caso de uso:** Crear una clave primaria (ID único) para una tabla que no la tiene, lo cual es vital para el modelo de Power BI.
* **`Detectar Tipo de Dato` (Detect Data Type):**
  * PQ aplica este paso automáticamente. A veces es bueno eliminarlo y establecer tus propios tipos de dato manualmente al final de la consulta para tener más control.

