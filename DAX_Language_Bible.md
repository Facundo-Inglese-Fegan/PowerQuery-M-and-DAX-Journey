# 📖 La Biblia de DAX

> **Nota:** Bienvenido al "cerebro" de Power BI. Hemos dejado la "cocina" (Power Query), donde los datos se *transforman* y se *cargan*. Ahora estamos en el motor de análisis (DAX), donde los datos *ya cargados* se *analizan* y se *calculan sobre la marcha*.
> DAX es un lenguaje de fórmulas, como Excel, pero con superpoderes para el modelado de datos. No modifica los datos de origen; calcula resultados en el momento.

---

## 1. ¿Qué es DAX (Data Analysis Expressions)?

DAX es el lenguaje de fórmulas usado por Power BI, Analysis Services y Power Pivot en Excel.

**La Analogía Central: El Menú Mágico del Restaurante:**

* **SQL** fue el *almacén* donde fuimos a buscar los ingredientes.
* **Power Query (M)** fue la *cocina* donde limpiamos, cortamos y preparamos esos ingredientes.
* **El Modelo de Datos** (las tablas cargadas) es la *despensa* con los ingredientes listos.
* **DAX** es el *menú inteligente*. Un cliente (usuario) se sienta y pide: "Quiero el total de ventas *solo de España* y *del último mes*". DAX es el motor que va a la despensa, toma los ingredientes relevantes (filtrados por España y último mes) y te da el número (el total) *al instante*.

DAX no "almacena" el total de ventas de España. Almacena la **fórmula** (`Total Ventas = SUM(Ventas[Monto])`) y la calcula cuando el usuario la necesita, basándose en los filtros que haya aplicado.

---

## 2. Los 3 Lugares Donde Vive DAX

Puedes escribir fórmulas DAX en tres lugares. Entender la diferencia es fundamental.

1. **Medidas (Measures):** El 90% de tu trabajo en DAX. Son cálculos dinámicos.
2. **Columnas Calculadas (Calculated Columns):** Columnas que añades a tus tablas *después* de cargarlas.
3. **Tablas Calculadas (Calculated Tables):** Tablas enteras que creas con DAX (ej. una tabla de calendario).

---

## 3. El Concepto Clave: Medidas vs. Columnas Calculadas

Este es el concepto **más importante** de DAX. Si entiendes esto, has entendido el 50% de DAX.

**Analogía: El Cuaderno de Notas del Profesor:**

Imagina una tabla de `Estudiantes` con sus notas.

### 3.1. Columna Calculada (Calculated Column)

* **Qué es:** Una columna que se añade *físicamente* a la tabla en el modelo.
* **Cuándo se calcula:** Durante la **actualización (refresh)** de los datos. Se calcula **fila por fila**.
* **Contexto:** `Contexto de Fila` (Row Context). La fórmula "ve" solo la fila actual.
* **Almacenamiento:** Ocupa espacio en la **RAM** (comprime muy bien, pero ocupa).
* **Analogía:** El profesor mira la nota de cada estudiante (ej. `[Nota] = 8`) y escribe al lado, en una nueva columna en su cuaderno, "Aprobado" (ej. `IF([Nota] >= 5, "Aprobado", "Suspendido")`). Esta etiqueta "Aprobado" se queda **escrita y guardada** en el cuaderno.
* **Uso:** Para **categorizar** o **filtrar**. Ideal para usar en un `SLICER` (filtro) o en la `Leyenda` de un gráfico.
* **Sintaxis (en la pestaña "Datos" de PBI):**

    ```dax
    Estado Alumno = IF(Estudiantes[Nota] >= 5, "Aprobado", "Suspendido")
    ```

### 3.2. Medida (Measure)

* **Qué es:** Un cálculo *virtual* que **no** se almacena en la tabla.
* **Cuándo se calcula:** **Al momento (on-the-fly)**, cuando la arrastras a un visual o un filtro la afecta.
* **Contexto:** `Contexto de Filtro` (Filter Context). La fórmula "ve" todos los filtros aplicados en el reporte (slicers, filas/columnas del visual, otros gráficos).
* **Almacenamiento:** No ocupa RAM. Ocupa **CPU** al momento del cálculo.
* **Analogía:** El profesor **no** escribe la "Nota Promedio de la Clase" en el cuaderno. Simplemente, cuando el director le pregunta, el profesor mira *todas* las notas y calcula el promedio en su cabeza (`AVERAGE([Nota])`). Si el director dice "¿Y el promedio *solo* de los "Aprobados"?", el profesor (DAX) *vuelve a calcular* el promedio al instante, pero *solo* para los alumnos que coinciden con ese filtro.
* **Uso:** Para **agregar** o **calcular** valores. Ideal para mostrar en `Valores` de un gráfico (tarjetas, barras, etc.).
* **Sintaxis (en la pestaña "Reporte" o "Modelo" de PBI):**

    ```dax
    Nota Promedio = AVERAGE(Estudiantes[Nota])
    ```

> **La Regla de Oro:**
>
> * Usa una **Columna Calculada** si necesitas *filtrar* o *segmentar* por el resultado (ej. "Grupo de Edad", "Categoría de Precio"). Piensa: "¿Voy a poner esto en un Slicer?".
> * Usa una **Medida** para todo lo demás, especialmente agregaciones (Suma, Promedio, Conteo). Piensa: "¿Voy a poner esto en una tarjeta de KPI o en el valor de un gráfico de barras?".

---

## 4. Sintaxis Básica de DAX

La sintaxis de DAX es muy similar a la de las fórmulas de Excel.

* **Referenciar Columnas:** Siempre debes incluir el nombre de la tabla.
  * **Mal:** `= SUM(Nota)`
  * **Bien:** `= SUM(Estudiantes[Nota])`
* **Operadores:** `+`, `-`, `*`, `/`, `^` (potencia), `&` (concatenar texto).
* **Lógicos:** `&&` (AND), `||` (OR), `NOT()`.
* **Comparación:** `=`, `<>`, `>`, `<`, `>=`, `<=`, `IN`.

---

## 5. Funciones Esenciales (Agregadores Simples)

Son la base de casi todas las medidas. No puedes sumar una columna sin `SUM()`.

* **`SUM(NombreTabla[Columna])`:** Suma todos los números de una columna.
* **`AVERAGE(NombreTabla[Columna])`:** Calcula el promedio.
* **`MIN(NombreTabla[Columna])`:** Encuentra el valor mínimo.
* **`MAX(NombreTabla[Columna])`:** Encuentra el valor máximo.
* **`COUNT(NombreTabla[Columna])`:** Cuenta el número de filas que tienen un valor (numérico o texto).
* **`COUNTA(NombreTabla[Columna])`:** Similar a `COUNT`.
* **`COUNTROWS(NombreTabla)`:** Simplemente cuenta el número total de filas de una tabla.
* **`DISTINCTCOUNT(NombreTabla[Columna])`:** ¡Muy importante! Cuenta el número de **valores únicos** (ej. "Contar clientes únicos").

---

## 6. Introducción al "Contexto" (La Magia de DAX)

El "Contexto" es el concepto central que hace que DAX funcione. Lo veremos a fondo en la Parte 2, pero aquí tienes la idea:

Una medida como `Total Ventas = SUM(Ventas[Monto])` no da un solo número. Da un número *diferente* dependiendo de dónde la pongas.

**Analogía: El Camaleón**
La medida `Total Ventas` es un camaleón.

* Si la pones en una **Tarjeta (KPI)**, ve el *contexto* de "todo el reporte" y te da el total general.
* Si la pones en una **Tabla** al lado de `[País]`, la medida cambia de color (valor) en cada fila. Para la fila "España", se calcula en el *contexto* de "España". Para la fila "Francia", se recalcula en el *contexto* de "Francia".

Los dos tipos de contexto que debes conocer son:

1. **Contexto de Fila (Row Context):** "La fila actual". Usado por columnas calculadas.
2. **Contexto de Filtro (Filter Context):** "Los filtros actuales". Usado por medidas.

---

## 7. `CALCULATE()`: La Función Más Importante de DAX

**¿Qué hace?** `CALCULATE()` evalúa una expresión (como `SUM()`) en un **contexto de filtro modificado**.

**La Analogía del Salón de Baile (Contexto de Filtro):**

* Tu reporte de Power BI es un gran **salón de baile**.
* Los **filtros** (slicers, gráficos) son la "música" que suena en el salón. Si el usuario filtra por "España" y "2024", la música es "Salsa de España 2024".
* Una **Medida** normal (ej. `Total Ventas = SUM(Ventas[Monto])`) "baila" al son de esa música. Solo calcula con los datos que coinciden con "Salsa de España 2024".
* **`CALCULATE()`** es como un **"mago"** que crea una *burbuja insonorizada* mágica en medio del salón. Dentro de esa burbuja, el mago puede cambiar la música.
  * Puede poner "Rock" (`FILTER(...)`).
  * Puede poner "silencio" (`ALL(...)`).
  * Puede decir "la misma música que afuera, pero añade violines" (`KEEPFILTERS(...)`).
* `CALCULATE()` hace su cálculo *dentro* de la burbuja (con la música modificada) y luego grita el resultado fuera de la burbuja.

**Sintaxis:**
`CALCULATE( <expresión>, <filtro1>, <filtro2>, ... )`

* **`<expresión>`:** Lo que quieres calcular (ej. `SUM(Ventas[Monto])`). Es el primer argumento, y es obligatorio.
* **`<filtro...>`:** Los modificadores del contexto. Aquí es donde ocurre la magia.

**Ejemplo 1: El Filtro Simple**
Quieres una medida que *siempre* muestre las ventas de España, sin importar lo que el usuario filtre por país.

```dax
Ventas España =
CALCULATE(
    SUM(Ventas[Monto]),
    dim_Clientes[País] = "España" // Modificador: Ignora el filtro de País de fuera y usa este.
)
```

* Si pones Total Ventas y Ventas España en una tabla por país: | País | Total Ventas | Ventas España | | :--- | :--- | :--- | | España | 1,000 | 1,000 | | Francia | 800 | 1,000 | | México | 1,200 | 1,000 | | Total| 3,000 | 1,000 |
* Total Ventas "obedece" al contexto de la fila. Ventas España lo ignora y aplica su propio filtro

---

## 8. Modificadores de Contexto (Las "Palancas" de CALCULATE)

Las palancas que le pasas a CALCULATE() no son solo filtros simples. Las más poderosas son funciones de tabla como ALL, FILTER y ALLEXCEPT.

### 8.1. ALL(): El Quita-Filtros

ALL() elimina los filtros de una tabla o columna.

**Analogía:** Es el "pase VIP" que le dice a CALCULATE: "Ignora a los guardias de seguridad (filtros) de esta puerta (tabla/columna)".

**Caso de Uso Clásico:** Porcentaje del Total Para calcular Ventas / Ventas Totales, necesitas que el denominador ignore los filtros.

```dax
Total Ventas = SUM(Ventas[Monto])

// 1. Creamos el denominador (Ventas Totales sin importar el producto)
Total Ventas Global =
CALCULATE(
    [Total Ventas], // La expresión
    ALL(dim_Productos) // El modificador: "ignora cualquier filtro de la tabla Productos"
)

// 2. Creamos la medida final
% Ventas s/ Total Producto =
DIVIDE(
    [Total Ventas],
    [Total Ventas Global]
)
```

* Ahora, en una tabla de productos: | Producto | Total Ventas | % Ventas s/ Total Producto | | :--- | :--- | :--- | | Leche | 1,000 | 25% | | Pan | 500 | 12.5% | | Huevos | 2,500 | 62.5% | | Total| 4,000 | 100% | (El Total Ventas Global invisible es 4,000 para todas las filas)

### 8.2. FILTER(): El Filtro Complejo

A veces, un filtro simple como [País] = "España" no es suficiente. Necesitas una lógica más compleja. FILTER() te permite usarla.

**Analogía:** FILTER() es un "inspector" que recorre una tabla fila por fila (creando un Contexto de Fila) y crea una lista de "aprobados" que luego le pasa a CALCULATE().

**Caso de Uso:** Ventas solo de "Productos Caros" (ej. Precio > 100).

```dax
Ventas Productos Caros =
CALCULATE(
    [Total Ventas],
    FILTER(
        dim_Productos, // La tabla que el "inspector" recorre
        dim_Productos[PrecioUnitario] > 100 // La regla que aplica fila a fila
    )
)
```

* Diferencia clave: FILTER crea un contexto de fila para evaluar su condición.

---

## 9. Iteradores (SUMX, AVERAGEX...): La Venganza del Contexto de Fila

Recuerdas que las medidas no tienen contexto de fila? Bueno, a veces lo necesitas. ¿Qué pasa si quieres calcular SUM(Ventas[Cantidad] *Ventas[PrecioUnitario])? NO PUEDES hacer SUM(Ventas[Cantidad])* SUM(Ventas[PrecioUnitario]). ¡El resultado es matemáticamente incorrecto!

* Necesitas una función que:
    1. Vaya fila por fila a la tabla Ventas (creando un Contexto de Fila).
    2. Calcule [Cantidad] * [PrecioUnitario] para esa fila.
    3. Guarde el resultado.
    4. Cuando termine, sume todos los resultados guardados.

* Esas son las funciones "X", o Iteradores.
  * SUMX(<table>, <expresión_fila_a_fila>)
  * AVERAGEX(<table>, <expresión_fila_a_fila>)
  * COUNTX(...), MINX(...), MAXX(...)
  * FILTER(...) (¡que ya vimos!) es también un iterador.

* Ejemplo:

```dax
Total Ventas (Correcto) =
SUMX(
    Ventas, // 1. La tabla a iterar
    Ventas[Cantidad] * Ventas[PrecioUnitario] // 2. La expresión a calcular en cada fila
)
```

---

## 10. Transición de Contexto (El Concepto "Gurú")

Aquí es donde todo se une. ¿Qué pasa si usas un CALCULATE() dentro de un iterador (SUMX) o una Columna Calculada?

**La Regla Mágica:** CALCULATE transforma cualquier Contexto de Fila que exista a su alrededor en un Contexto de Filtro.

**Analogía:** El Foco de Interrogación

* Estás en un SUMX, recorriendo la tabla Ventas fila por fila (Contexto de Fila).
* Estás en la fila: ID=101, Producto="Leche", Cantidad=2.
* Si en tu SUMX llamas a CALCULATE([Total Clientes Unicos])...
* CALCULATE enciende un "foco" sobre ti. "Ahá!", dice. "Estás en la fila ID=101, Producto='Leche', Cantidad=2".
* Inmediatamente, transforma esa información en un filtro (...WHERE Producto='Leche' AND Cantidad=2...) y se lo aplica a [Total Clientes Unicos].
* Esto se llama Transición de Contexto.

* Ejemplo (en una Columna Calculada en dim_Productos): Quieres una columna que muestre las ventas totales de ese producto.
  * Fórmula MALA: Col_Ventas = SUM(Ventas[Monto])
    * Resultado: ¡Todas las filas mostrarán el mismo número (el total general)!
    * Por qué: El Contexto de Fila (producto "Leche") no se propaga a la tabla Ventas. SUM solo ve el Contexto de Filtro (que está vacío).
  * Fórmula BUENA: Col_Ventas = CALCULATE(SUM(Ventas[Monto]))
    * Resultado: Cada fila muestra las ventas de ese producto.
    * Por qué: CALCULATE activa la Transición de Contexto. La fila "Leche" se transforma en un filtro dim_Productos[Producto] = "Leche", que filtra la tabla Ventas y luego se ejecuta el SUM.

---

## 11. El Requisito Innegociable: La Tabla de Calendario

DAX no puede "viajar en el tiempo" si no le das un "mapa del tiempo" fiable. Las funciones de Inteligencia de Tiempo **NO FUNCIONARÁN** si dependes de la columna de fecha de tu tabla de `Ventas`.

**¿Por qué?** Tu tabla de `Ventas` tiene "huecos". Probablemente no tienes ventas los domingos, o en festivos. Para DAX, esto es un mapa roto.

Necesitas una **Tabla de Calendario** (o `dim_Calendario`) separada que cumpla estas reglas:

1. **Es Única:** Una fila por cada día. Sin duplicados.
2. **Es Continua:** **Sin huecos.** Debe tener *todos* los días desde el 1 de enero de tu primer año hasta el 31 de diciembre de tu último año, aunque esos días no tuvieras ventas.
3. **Relacionada:** Debe tener una relación de **Uno-a-Muchos** (`1:*`) desde `dim_Calendario[Fecha]` (el lado "Uno") hasta tu tabla de hechos `Ventas[FechaVenta]` (el lado "Muchos").
4. **Marcada:** Debes decirle a Power BI que es tu tabla de calendario oficial (Clic derecho en la tabla -> `Marcar como tabla de fechas`).

**Analogía: La Regla de Medir:**

* **Tu tabla de `Ventas`:** Es como una regla de medir rota a la que le faltan centímetros. No puedes confiar en ella para medir "30 días atrás".
* **Tu `dim_Calendario`:** Es una regla de medir perfecta, de titanio, con cada milímetro marcado. DAX *necesita* esta regla para funcionar.

**¿Cómo crearla?**
Puedes crearla en SQL, en Power Query (M), o directamente en DAX (¡como una **Tabla Calculada**!):

```dax
dim_Calendario =
ADDCOLUMNS (
    -- 1. Crea la base de fechas continuas
    CALENDAR ( DATE ( 2020, 1, 1 ), DATE ( 2025, 12, 31 ) ),
    -- 2. Añade columnas útiles (Contexto de Fila)
    "Año", YEAR ( [Date] ),
    "Mes Num", MONTH ( [Date] ),
    "Nombre Mes", FORMAT ( [Date], "mmmm" ),
    "Trimestre", "Q" & FORMAT ( [Date], "q" ),
    "Año-Mes", FORMAT ( [Date], "yyyy-mm" )
)
```

**Regla de Oro:** Una vez que tengas tu dim_CalendARIO, TODOS tus filtros y slicers de fecha deben usar las columnas de dim_Calendario (ej. dim_Calendario[Año], dim_Calendario[Nombre Mes]), NUNCA las columnas de fecha de la tabla Ventas.

---

## 12. La Magia: Funciones de Inteligencia de Tiempo

Todas las funciones de Inteligencia de Tiempo son "azúcar sintáctico". Son atajos para una función CALCULATE + FILTER mucho más compleja.

Toman tu contexto de filtro actual (ej. "Marzo 2024") y te devuelven un nuevo contexto de filtro de tiempo (ej. "Marzo 2023").

### 12.1. Comparaciones Año-sobre-Año (YoY)

Esta es la más famosa.

```dax
Ventas Año Anterior (YoY) =
CALCULATE (
    [Total Ventas],
    SAMEPERIODLASTYEAR ( dim_Calendario[Fecha] )
)
```

* ¿Qué hace SAMEPERIODLASTYEAR? Mira el contexto actual (ej. el mes de "Marzo 2024"). Viaja un año atrás y devuelve el conjunto de fechas equivalentes: "Marzo 2023".
* CALCULATE usa esa nueva tabla de fechas como su filtro.

### 12.2. Moviéndose en el Tiempo (DATEADD)

DATEADD es la función más flexible para moverte.

```dax
Ventas Mes Anterior (MoM) =
CALCULATE (
    [Total Ventas],
    DATEADD ( dim_Calendario[Fecha], -1, MONTH ) // Moverse -1 MES
)

Ventas Trimestre Anterior (QoQ) =
CALCULATE (
    [Total Ventas],
    DATEADD ( dim_Calendario[Fecha], -1, QUARTER ) // Moverse -1 TRIMESTRE
)
```

* Sintaxis: DATEADD(<columna_fecha>, <numero_intervalos>, <intervalo>)
* <intervalo>: DAY, MONTH, QUARTER, YEAR.

### 12.3. Acumulados (YTD, QTD, MTD)

Estas funciones calculan el total acumulado desde el inicio del período hasta la fecha actual.

```dax
Ventas Acumuladas Año (YTD) =
CALCULATE (
    [Total Ventas],
    DATESYTD ( dim_Calendario[Fecha] )
)
```

* ¿Qué hace DATESYTD? Mira el contexto actual (ej. "15 de Marzo de 2024"). Devuelve un conjunto de fechas desde el "1 de Enero de 2024" hasta el "15 de Marzo de 2024".
* Existen también DATESQTD (Quarter-to-Date) y DATESMTD (Month-to-Date).

Forma Alternativa (Shortcut): DAX también tiene funciones "TOTAL" que incluyen el SUM dentro.

```dax
Ventas Acumuladas Año (YTD) v2 =
TOTALYTD ( SUM ( Ventas[Monto] ), dim_Calendario[Fecha] )
```

> Ambas fórmulas dan el mismo resultado. La primera (con CALCULATE) es más "pura" y te permite reutilizar tu medida base [Total Ventas].

---

## 13. Uniendo Todo: El Reporte de Crecimiento

Ahora puedes construir una tabla que muestre el crecimiento:

```dax
// 1. Medida base
Total Ventas = SUM ( Ventas[Monto] )

// 2. Medida de tiempo
Ventas Año Anterior =
CALCULATE (
    [Total Ventas],
    SAMEPERIODLASTYEAR ( dim_CalendARIO[Fecha] )
)

// 3. Medida de crecimiento
Crecimiento YoY % =
DIVIDE (
    [Total Ventas] - [Ventas Año Anterior],
    [Ventas Año Anterior]
)
```

* Si pones esto en una matriz con dim_Calendario[Año] y dim_Calendario[Nombre Mes]: | | 2023 | 2024 | Crecimiento YoY % | | :--- | :--- | :--- | :--- | | Enero | 1000 | 1100 | 10.0% | | Febrero | 900 | 1000 | 11.1% | | Marzo | 1100 | 1050 | -4.5% |
* ¡Has creado un análisis de crecimiento dinámico!

---

## 14. Variables (`VAR...RETURN`): Tu Mejor Amigo

Las variables son **esenciales** para escribir DAX complejo. NO son opcionales si quieres ser un experto.

**¿Qué hacen?** Te permiten almacenar el resultado de un cálculo en una "caja" (variable) con nombre, para poder usar esa caja múltiples veces *después*.

**Analogía:** Es como preparar los ingredientes *antes* de cocinar. En lugar de ir a la nevera (`[Total Ventas]`) 10 veces, lo sacas una vez, lo pones en un bol (`VAR vVentas = [Total Ventas]`) y luego usas el bol.

**Sintaxis:**

```dax
NombreMedida =
VAR NombreVariable1 = ...
VAR NombreVariable2 = ...
RETURN
    <cálculo final que usa las variables>
```

Por qué son VITALES:

* Rendimiento: DAX solo calcula la variable una vez. Si escribes [Total Ventas] / [Ventas Año Anterior] en 5 sitios, DAX podría calcularlo 5 veces. Si lo guardas en una VAR, se calcula 1 vez y se reutiliza 5.
* Legibilidad: Descompone un problema complejo en pasos lógicos.
* Depuración (Debugging): Puedes cambiar temporalmente el RETURN para que te devuelva una variable intermedia (RETURN NombreVariable1) y así ver qué está calculando en ese paso.

Ejemplo (Rehaciendo Crecimiento YoY %):

```dax
Crecimiento YoY % (con VAR) =
VAR vVentasActuales = [Total Ventas]
VAR vVentasAnteriores = [Ventas Año Anterior]
VAR vCrecimiento = vVentasActuales - vVentasAnteriores
VAR vResultado = DIVIDE ( vCrecimiento, vVentasAnteriores )
RETURN
    vResultado
```

* ¡Mucho más limpio y fácil de depurar!

---

## 15. Manejo de Errores y Blancos (BLANK())

En DAX, los errores de "división por cero" son feos (Infinity, NaN). Debemos manejarlos.

* MAL: ... / [Ventas Año Anterior] (Si las ventas del año anterior son 0, esto explota).
* BIEN: DIVIDE(..., [Ventas Año Anterior])
* MEJOR: DIVIDE(..., [Ventas Año Anterior], 0) (o BLANK())

La función DIVIDE(<numerador>, <denominador>, [resultado_alternativo]) está optimizada y es segura contra la división por cero. Si omites el resultado alternativo, devuelve BLANK().

BLANK() vs. 0:

* BLANK() (vacío) es el ciudadano de primera clase en DAX.
* Analogía: 0 es un valor, es "cero euros". BLANK() es "no lo sé" o "no aplica".
* Ventaja: Los gráficos de Power BI (como las líneas) ignoran los BLANK(), creando líneas limpias. Si usas 0, la línea del gráfico se desplomará hasta el cero, ensuciando la visualización.
* Uso: Prefiere BLANK() como resultado de DIVIDE o para medidas que no deben mostrarse (ej. IF ( [Total Ventas] > 1000, [Total Ventas], BLANK() )).

---

## 16. Manipulación de Relaciones (USERELATIONSHIP)

¿Qué pasa si tu tabla Ventas tiene DOS columnas de fecha?

* FechaPedido
* FechaEntrega

Solo puedes tener una relación activa a tu dim_Calendario. Probablemente Ventas[FechaPedido] -> dim_Calendario[Fecha]. La otra (Ventas[FechaEntrega]) debe ser inactiva (aparece como una línea discontinua).

¿Cómo calculas las ventas por Fecha de Entrega? Le dices a CALCULATE que, solo para ese cálculo, ignore la relación activa y use la inactiva.

```dax
Ventas por Fecha de Pedido = [Total Ventas] // (Usa la relación activa por defecto)

Ventas por Fecha de Entrega =
CALCULATE (
    [Total Ventas],
    // "Oye, ¡usa esta otra relación en su lugar!"
    USERELATIONSHIP ( Ventas[FechaEntrega], dim_Calendario[Fecha] )
)
```

Esto te permite tener un solo slicer de dim_Calendario que filtre ambas medidas, una por fecha de pedido y otra por fecha de entrega.

## 17. Modificadores de Contexto Avanzados

**KEEPFILTERS():**

* CALCULATE([Total Ventas], dim_Clientes[País] = "España")
  * Qué hace: Sobrescribe. "¡Olvida cualquier filtro de país y usa 'España'!"
* CALCULATE([Total Ventas], KEEPFILTERS(dim_Clientes[País] = "España"))
  * Qué hace: Intersecta. "Toma el filtro de país que ya existe y además aplica 'España'".
* Caso de uso: Si el usuario filtró "Francia" en un slicer, la primera medida (sin KEEPFILTERS) le mostrará "España" (confuso). La segunda (con KEEPFILTERS) le mostrará BLANK() (porque País = "Francia" Y País = "España" es imposible). Es más correcto.

**ALLEXCEPT():**
Es un atajo para ALL.

* ALL(dim_Productos): Quita TODOS los filtros de la tabla dim_Productos.
* ALLEXCEPT(dim_Productos, dim_Productos[Categoria]):
  * Traducción: "Quita TODOS los filtros de dim_Productos... EXCEPTO el filtro que esté aplicado a la columna [Categoria]".
* Caso de uso: Calcular el % del Total de la Categoría. Quieres ignorar el filtro del Producto pero mantener el filtro de la Categoria.

---

## 18. Funciones de Tabla (Virtuales)

A veces, para un cálculo, necesitas crear una tabla "virtual" en memoria.

* SUMMARIZE(<table>, <columnas_agrupar>, [nombre_nueva_col], [expresion]):
  * El GROUP BY de DAX. Crea una tabla virtual resumida.
* ADDCOLUMNS(<table>, <nombre_nueva_col>, <expresion>):
  * Añade una columna a una tabla virtual (¡respeta el contexto de fila!).
* FILTER(<table>, <condicion>):
  * Devuelve una tabla virtual filtrada.

No las usarás para crear medidas simples, pero son la base de los cálculos más complejos, donde creas una tabla virtual (VAR vTabla = ...) y luego la iteras (RETURN SUMX(vTabla, ...)).


