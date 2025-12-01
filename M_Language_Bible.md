# 📖 La Biblia del Lenguaje M

> **M** (cuyo nombre real es "Power Query Formula Language") es el lenguaje de programación funcional que impulsa a Power Query. Dominarlo te da superpoderes para realizar transformaciones personalizadas que son imposibles desde la interfaz.
> **Nota:** Bienvenido al "detrás de cámaras". Cada clic que hiciste en la Biblia de Power Query estaba, en secreto, escribiendo una línea de este código.
> **Nota 2:** Luego de la sección 6, conocerás la estructura `let...in` y los tipos de datos. A partir de la sección 7 vas a usar las "herramientas" que M nos da. La interfaz de Power Query es, en realidad, un "constructor visual de funciones". Cuando haces clic en "Quitar Columnas", por debajo estás llamando a la función `Table.RemoveColumns`.
> **Nota 3:** Luego de haber aprendido la sintaxis, usado la biblioteca estándar y dominado `each` pasarás a la sección 10. Aquí vas a construir tus propias herramientas. Una función personalizada es, simplemente, una "consulta" (una receta) a la que le has puesto un nombre y le has dicho qué "ingredientes" (parámetros) necesita recibir.

---

## 1. ¿Qué es M? La "Receta" Escrita

Si la interfaz de Power Query es como un "asistente de cocina" que te guía con botones ("cortar", "mezclar", "hornear"), **M es la receta real escrita** que el asistente está leyendo.

La barra de fórmulas en Power Query te muestra el código M del *paso seleccionado*. El "Editor Avanzado" te muestra el código M de la *consulta completa*.

**Analogía Central:**

* **Interfaz PQ:** Una app de cocina con botones.
* **Editor Avanzado (M):** El archivo `receta.txt` que contiene todos los ingredientes y pasos en orden.

---

## 2. La Estructura Clave: `let ... in`

Casi toda consulta M que veas tendrá esta estructura. Es el 99% de la sintaxis que necesitas reconocer.

**Analogía:** Es un bloque de construcción de LEGO.

* `let`: Es la parte donde "declaras tus piezas" (variables, pasos).
* `in`: Es la parte donde "muestras la pieza final" (el resultado que quieres devolver).

```m
let
    // 'let' es donde defines todos tus pasos (variables)
    // Cada paso termina con una coma (,) excepto el último

    // Paso 1: Definir un texto
    MiTexto = "Hola",

    // Paso 2: Convertir a mayúsculas (basado en el paso anterior)
    TextoMayusculas = Text.Upper(MiTexto),

    // Paso 3: Añadir más texto
    ResultadoFinal = TextoMayusculas & " MUNDO"

in
// 'in' es lo que la consulta devolverá.
// Usualmente, es el nombre del último paso.
    ResultadoFinal 
/// Resultado de esta consulta: "HOLA MUNDO"
```

---

## 3. Las 4 Reglas de Sintaxis

* Es Sensible a Mayúsculas (Case-Sensitive):
  * MiTexto NO es lo mismo que mitexto.
  * Text.Upper NO es lo mismo que text.upper.
  * ¡Esto es la fuente #1 de errores para usuarios de SQL (que no es case-sensitive)!

* Nombres de Variables/Pasos:
  * No pueden contener espacios, a menos que uses un formato especial.
  * Formato estándar: MiVariable (CamelCase).
  * Formato con espacios (el que usa la UI): #"Mi Variable con Espacios"
  * La interfaz de PQ usa #"Nombre del Paso" automáticamente (ej. #"Columnas quitadas"). Tú puedes usar nombres simples (como Paso1) para que sea más legible.

* Comentarios:
  * // Esto es un comentario de una línea
  * /*Esto es un comentario de múltiples líneas*/
  * ¡Usa comentarios! Documenta tu código M complejo.

* Inmutabilidad:
  * Las "variables" en M no son como en otros lenguajes. No puedes "cambiar" un valor.
  * Una vez que defines MiTexto = "Hola", MiTexto es "Hola" para siempre.
  * Lo que haces es crear un nuevo paso (una nueva variable) basado en el anterior, como vimos con TextoMayusculas. Es una cadena de transformaciones.

---

## 4. Tipos de Datos Primitivos

Son los bloques de construcción más básicos.

* text (Texto): "Hola Mundo"
* number (Número): 123, 123.45
* logical (Lógico): true, false (¡siempre en minúsculas!)
* date (Fecha): #date(YYYY, MM, DD) -> #date(2025, 10, 24)
* datetime (Fecha y Hora): #datetime(YYYY, MM, DD, HH, MM, SS) -> #datetime(2025, 10, 24, 22, 58, 0)
* duration (Duración): #duration(DD, HH, MM, SS) -> #duration(1, 12, 30, 0) (1 día, 12 horas, 30 min)
* null (Nulo): null (¡minúscula!) representa la ausencia de valor.

---

## 5. Tipos de Datos Estructurados (¡Importante!)

Aquí es donde reside el poder. Todo en M es, en el fondo, uno de estos.

### 5.1. list (Lista)

Una secuencia ordenada de valores. Como una sola columna de Excel o un array.

* Sintaxis: Se define con llaves { }.
* Ejemplo:

```m
let
    MiLista = {1, 2, 3, "Hola", null, #date(2025, 1, 1)}
in
    MiLista
```

* Acceso: Las listas se basan en un índice cero (el primer elemento es el 0).
  * MiLista{0} devolvería 1.
  * MiLista{3} devolvería "Hola".

### 5.2. record (Registro)

Un conjunto de pares Nombre = Valor. Como una sola fila de una tabla o un objeto JSON.

* Sintaxis: Se define con corchetes [ ].
* Ejemplo:

```m
let
    MiRegistro = [
        Nombre = "Ana",
        Edad = 30,
        Pais = "España"
    ]
in
    MiRegistro
```

* Acceso: Se accede por el nombre (clave).
  * MiRegistro[Nombre] devolvería "Ana".

### 5.3. table (Tabla)

Una colección de listas (columnas) y registros (filas).

* Sintaxis (Creación Manual): #table(columnas, filas)
  * columnas es una list de textos.
  * filas es una list de list (donde cada sub-lista es una fila).
* Ejemplo:

```m
let
    MiTabla = #table(
        // 1. Definir columnas
        {"ID", "Producto", "Precio"},

        // 2. Definir filas (lista de listas)
        {
            {1, "Leche", 1.5},
            {2, "Pan", 0.8},
            {3, "Huevos", 2.1}
        }
    )
in
    MiTabla
```

---

## 6. Operadores y Condicionales (if)

### 6.1. Operadores Comunes

* Texto: & (para concatenar) -> "Hola" & " " & "Mundo"
* Números: +, -, *, /
* Lógicos: and, or, not (¡minúsculas!)
* Comparación: =, <>, >, <, >=, <=

### 6.2. La Expresión if

La sintaxis es muy parecida a la de Excel, pero sensible a mayúsculas.

* Sintaxis: if [condicion] then [resultado_si_true] else [resultado_si_false]
* Ejemplo:

```m
let
    Edad = 25,
    Categoria = if Edad >= 18 then "Adulto" else "Menor"
in
    Categoria
```

> ¡OJO! El else es obligatorio. No puedes tener un if sin un else.

---

## 7. La Biblioteca de Funciones Estándar

M viene con una biblioteca masiva de funciones preconstruidas. **No necesitas memorizarlas**, solo necesitas saber que existen y cómo encontrarlas. Los nombres son muy intuitivos y se agrupan por el tipo de dato que manipulan:

* **`Text.` (Funciones de Texto):**
  * `Text.Upper(texto)`: Pasa a mayúsculas.
  * `Text.Trim(texto)`: Quita espacios al inicio y al final.
  * `Text.Split(texto, separador)`: Divide un texto en una `list`.
  * `Text.Contains(texto, subtexto)`: Devuelve `true` o `false`.

* **`Number.` (Funciones de Número):**
  * `Number.Round(numero, digitos)`: Redondea.
  * `Number.IsEven(numero)`: Devuelve `true` o `false`.
  * `Number.From(valor)`: Intenta convertir un valor a número (¡muy útil!).

* **`List.` (Funciones de Lista):**
  * `List.Sum(lista)`: Suma una lista de números.
  * `List.Distinct(lista)`: Devuelve una lista con valores únicos.
  * `List.Count(lista)`: Cuenta los elementos.
  * `List.Transform(lista, funcion)`: Aplica una transformación a cada ítem de la lista.

* **`Table.` (Las más usadas):**
  * `Table.SelectRows(tabla, condicion)`: El `WHERE` de SQL.
  * `Table.AddColumn(tabla, nombreNuevaCol, funcionGeneradora)`: Añade una columna.
  * `Table.Group(tabla, claveAgrupacion, camposAgregados)`: El `GROUP BY` de SQL.
  * `Table.Join(tabla1, clave1, tabla2, clave2, tipoJoin)`: El `JOIN` de SQL.

* **`Date.`, `Time.`, `DateTime.`, `Record.`, `Error.`...**

**¿Cómo las usa la UI?**
Observa este ejemplo simple.

**Paso 1: `Origen`** (Conectarse a la tabla)
`Origen = MiTablaManual`

**Paso 2: `Filas filtradas`** (Haces clic en la UI para filtrar `Precio > 1`)
`FilasFiltradas = Table.SelectRows(Origen, each [Precio] > 1)`

**Paso 3: `Columna agregada`** (Haces clic en "Agregar Columna" para crear "PrecioConIVA")
`PrecioConIVA = Table.AddColumn(FilasFiltradas, "PrecioConIVA", each [Precio] * 1.21)`

Como ves, la UI simplemente "escribe" el siguiente paso de M, usando el paso anterior como primer argumento de la función. Ahora, vamos a la parte más confusa de ese código: la palabra `each`.

---

## 8. La Palabra Clave `each` (El Corazón de M)

`each` es una abreviatura para crear una **función simple sobre la marcha**.

**Analogía:** `each` es un **"robot de fábrica"** al que le das una instrucción simple.
Imagina una cinta transportadora (`Table.AddColumn`) que te envía cajas (filas). `each` es el robot que está al lado de la cinta. Le dices:
`each [Precio] * 1.21`
Y el robot, *por cada caja (fila) que pasa*, abre la caja, saca el `[Precio]`, lo multiplica por 1.21, y lo pone en una nueva etiqueta llamada "PrecioConIVA".

**Técnicamente:**
`each` define una función que toma un parámetro (por defecto llamado `_`) y hace algo con él.

* `each [Precio] * 1.21`
    ...es la forma corta de escribir...
* `(_) => _[Precio] * 1.21`

Donde:

* `_` (guion bajo) representa "la fila actual" (que es un `record`).
* `_[Precio]` significa: "del registro actual (`_`), dame el valor del campo `Precio`".
* `[]` (corchetes) es la forma de M de acceder a un campo de un registro. `_[Precio]` es lo mismo que `[Precio]` cuando `_` está implícito (como después de un `each`).

### `each` en `Table.SelectRows` (Filtrar)

`Table.SelectRows(MiTabla, each [Pais] = "España")`

* **Traducción:** "Robot `each`, mira cada fila (`_`). Si el campo `[Pais]` de esa fila es igual a `"España"`, devuelve `true` (mantén la fila). Si no, devuelve `false` (descarta la fila)."

### `each` en `Table.AddColumn` (Añadir Columna)

`Table.AddColumn(MiTabla, "Categoria", each if [Ventas] > 100 then "Alto" else "Bajo")`

* **Traducción:** "Robot `each`, mira cada fila (`_`). Toma el campo `[Ventas]`. Si es mayor a 100, devuelve el texto `"Alto"`. Si no, devuelve `"Bajo"`. El valor que devuelvas será el de la nueva columna `Categoria` para esa fila."

---

## 9. Manejo de Errores: `try ... otherwise`

En M, si una operación falla (ej. `Number.From("hola")`), la consulta entera se detiene con un `Error`. A veces, quieres "capturar" ese error y manejarlo.

**Analogía:** `try` es como "intentar" tocar algo que *podría* estar caliente.

* `try`: El intento.
* `otherwise`: Lo que haces si te quemas (si falla).

**Sintaxis:**
`try [operacion_que_puede_fallar] otherwise [valor_si_falla]`

* **¡OJO!** `try` devuelve un `record`. Si la operación tiene éxito, el `record` es `[HasError=false, Value=resultado]`. Si falla, es `[HasError=true, Error=detalle_del_error]`.

**Ejemplo Práctico:** Reemplazar errores en la UI es, por debajo, un `try...otherwise`.

```m
let
    // 1. Una tabla con un valor "malo"
    Origen = #table(
        {"ID", "TextoNumero"},
        {{1, "100"}, {2, "hola"}, {3, "200"}}
    ),

    // 2. Intentamos convertir. El paso 2 fallará.
    // ConversionDirecta = Table.TransformColumnTypes(Origen, {{"TextoNumero", type number}}), // <-- Esto daría ERROR

    // 3. Lo hacemos con try...otherwise
    ColumnaConvertida = Table.AddColumn(Origen, "Conversion", (fila) =>
        let
            // Por cada fila, INTENTAMOS convertir el valor
            Intento = try Number.From(fila[TextoNumero]),

            // Comprobamos el resultado del 'try'
            Resultado = if Intento[HasError] then
                            null  // Si falló, devuelve null
                        else
                            Intento[Value] // Si tuvo éxito, devuelve el valor
        in
            Resultado
    )
in
    ColumnaConvertida
```

> Versión corta (más común): ColumnaConvertida = Table.AddColumn(Origen, "Conversion", each try Number.From([TextoNumero]) otherwise null)
> Traducción: "Añade una columna 'Conversion'. Por cada fila, intenta convertir [TextoNumero]. Si lo logras, pon el resultado. Si no (otherwise), pon null."

---

## 10. ¿Qué es una Función Personalizada?

En M, todo es una expresión que devuelve un valor. Una función no es diferente. Es una expresión que **recibe parámetros** y **devuelve un valor**.

**Analogía:** Es una **"Máquina Expendedora"** personalizada.

* **Parámetros (Entrada):** Las monedas y el botón que presionas (ej. `(texto_sucio, longitud_maxima)`).
* **Cuerpo (Lógica):** La maquinaria interna que definiste (ej. `Text.Trim`, `Text.Start`).
* **Valor (Salida):** El producto que te entrega (ej. el texto limpio y recortado).

---

## 11. La Sintaxis para Crear una Función

La sintaxis es simple. Se define con `(parametros) => resultado`.

```m
(parametro1, parametro2) =>
    let
        // Tu lógica va aquí
        Paso1 = parametro1 + 10,
        Paso2 = Paso1 * parametro2
    in
        Paso2
```

* (): Los paréntesis contienen la lista de parámetros de entrada.
* =>: El "cohete". Se lee como "va a" o "resulta en". Indica el inicio de la lógica de la función.
* El let...in es opcional para funciones de una sola línea. Por ejemplo: (x, y) => x + y Esta es una función válida que suma dos números

---

## 12. Creación y Uso: Un Ejemplo Práctico

Vamos a crear una función que limpie un campo de texto: lo pasa a mayúsculas, le quita espacios y reemplaza null por un texto vacío.

### Paso 1: Crear la Función (como una Consulta)

1. En Power Query, haz clic derecho en el panel de consultas -> Nueva consulta -> Consulta en blanco.
2. Ponle un nombre descriptivo, ej. fn_LimpiarTexto.
3. Abre el Editor Avanzado y pega este código:

```m
(texto_sucio as any) =>
    let
        // 1. Convertir 'null' a texto vacío
        // Usamos 'as any' en el parámetro para poder chequear si es null
        Paso1 = if texto_sucio = null then "" else texto_sucio,

        // 2. Convertir a texto (por si era un número)
        Paso2 = Text.From(Paso1),

        // 3. Quitar espacios
        Paso3 = Text.Trim(Paso2),

        // 4. Poner en mayúsculas (Título)
        Paso4 = Text.Proper(Paso3)
    in
        Paso4
```

4. ¡Listo! Verás que el icono de la consulta cambia a (fx). Power Query ahora la reconoce como una función.

### Paso 2: Invocar (Usar) la Función

* Ahora, ve a otra consulta (ej. tu tabla Clientes) donde tengas columnas de texto sucias.
    1. Selecciona la tabla Clientes.
    2. Ve a Agregar Columna -> Columna personalizada.
    3. Usa esta fórmula:

```m
// Invocar nuestra función
fn_LimpiarTexto([NombreCliente])
```

* ¡Ya está! Has invocado tu función personalizada fn_LimpiarTexto, pasándole el valor de la columna [NombreCliente] de cada fila como parámetro.

> Ventaja: Ahora puedes usar fn_LimpiarTexto para limpiar [NombreCliente], [Direccion], [Ciudad], etc., y si alguna vez decides que la limpieza debe ser Text.Upper en lugar de Text.Proper, solo editas la función una vez y el cambio se aplica en todos lados.

---

## 13. El Visor Secreto: #shared

* ¿Cómo sabes qué funciones existen? ¿Cómo se llaman Table.SelectRows o Text.Upper?
* M tiene una "biblioteca" global incorporada. Puedes verla tú mismo.
    1. Crea una Consulta en blanco.
    2. Abre el Editor Avanzado.
    3. Escribe solo esto: #shared
    4. Dale a "Aceptar".

* El resultado es un record masivo que contiene todas las funciones, variables y valores globales disponibles en tu entorno de M, incluyendo tus propias funciones (fn_LimpiarTexto) y tus otras consultas.
  * ¡Es el diccionario definitivo! Si alguna vez te preguntas "¿habrá una función para...?", puedes buscarla aquí.
  * (Es más fácil usar la documentación oficial de Microsoft, pero #shared te demuestra que todo es accesible).

---

## 14. Documentación de Funciones

Puedes hacer que tus funciones sean más amigables (como las de Microsoft) añadiendo "metadatos".

```m
let
    // 1. La lógica de la función
    Funcion = (texto as text) => Text.Upper(texto),

    // 2. Los metadatos (la documentación)
    Documentacion = [
        Documentation.Name = "fn_TextoMayusculas",
        Documentation.Description = "Convierte un valor de texto a MAYÚSCULAS.",
        Documentation.Examples = {[
            Description = "Convierte 'hola' a 'HOLA'",
            Code = "fn_TextoMayusculas(""hola"")",
            Result = "HOLA"
        ]}
    ]
in
    // 3. Adjuntar la documentación a la función
    Value.ReplaceType(Funcion, type function (texto as text) as text meta Documentacion)
```

* Esto es avanzado, pero hace que cuando uses la función en la UI, te aparezca la ayuda y los ejemplos.

---

## 15. Tipado Estricto (El `type` keyword)

Hasta ahora, hemos dejado que M "infiera" los tipos de datos. Pero M tiene un sistema de tipos muy robusto. Puedes *declarar* la estructura exacta de tus datos.

**Analogía:** Es la diferencia entre un "tupper" (un contenedor genérico) y una "caja de herramientas" con el hueco exacto para cada martillo y destornillador.

* **¿Por qué?** Para forzar un esquema. Si esperas una tabla con `ID` (número) y `Nombre` (texto), puedes definirlo. Si los datos de origen cambian, M generará un error de tipo en lugar de fallar silenciosamente.

* **Sintaxis:**
    `type [NombreColumna = TipoDato, ...]`

* **Ejemplo:**

    ```m
    let
        // 1. Definimos un "tipo" de tabla personalizado
        MiEsquema = type table [
            ID = Int64.Type,
            Producto = Text.Type,
            Precio = Currency.Type,
            Fecha = Date.Type
        ],

        // 2. Creamos una tabla
        Datos = #table(
            {"ID", "Producto", "Precio", "Fecha"},
            {
                {1, "Leche", 1.5, #date(2025, 1, 1)},
                {2, "Pan", 0.8, #date(2025, 1, 2)},
                {3, "Manzanas", "2.1", "2025-01-03"} // ¡Valores incorrectos a propósito!
            }
        ),

        // 3. Forzamos la tabla a nuestro esquema
        // Value.ReplaceType() es la función clave
        TablaTipada = Value.ReplaceType(Datos, MiEsquema)
    in
        TablaTipada
    ```

* **Resultado:** En la fila 3, verás **errores** en las columnas `Precio` (porque "2.1" es texto, no `Currency.Type`) y `Fecha` (porque "2025-01-03" es texto, no `Date.Type`). Has creado un validador de esquemas.

---

## 16. Creación Explícita de Errores (`error`)

Ya vimos cómo *capturar* errores con `try...otherwise`. Pero a veces, tú quieres *crear* (lanzar) tu propio error.

**Analogía:** Eres el guardia de seguridad. En lugar de solo "ver" un problema (`try`), tienes la autoridad para "detener" a alguien (`error`) y decir exactamente por qué.

* **¿Por qué?** Para validaciones de negocio. Si una venta es superior a 10,000, quizás no es un error de tipo, pero sí un error de lógica que quieres detener y reportar.

* **Sintaxis:**
    `error [Message = "...", Detail = "..."]` (es un `record`)

* **Ejemplo:**

    ```m
    let
        Venta = 50000,

        Validacion = if Venta > 10000 then
                         error [
                             Message = "Venta fuera de límites",
                             Reason = "DataValidationError",
                             Detail = "La venta de " & Number.ToText(Venta) & " excede el máximo permitido de 10,000."
                         ]
                     else
                         "Venta OK",
        Resultado = Venta
    in
        Resultado
    ```

* **Resultado:** La consulta se detendrá y mostrará tu mensaje de error personalizado ("Venta fuera de límites"), que es mucho más útil que un `null` o un error genérico.

---

## 17. Funciones Recursivas (`@`)

M permite que una función se llame a sí misma. Esto es un concepto de programación avanzado, útil para "caminar" por estructuras jerárquicas (como un organigrama o una lista de carpetas).

* **Sintaxis:** Usas el operador `@` (arroba) para referirte a la propia función *dentro* de ella misma.

* **Ejemplo Clásico (Factorial):**

    ```m
    let
        // fn_Factorial se llama a sí misma (@fn_Factorial)
        fn_Factorial = (n as number) as number =>
            if n <= 1 then
                1
            else
                n * @fn_Factorial(n - 1)
    in
        fn_Factorial(5) // Resultado: 120 (5*4*3*2*1)
    ```

* **Caso de uso real:** Recorrer una jerarquía de empleados (Jefe -> Empleado -> Empleado) para encontrar todos los subordinados en cualquier nivel.

---

## 18. `Section` (Documentos M)

Esto es más de arquitectura. Cuando trabajas en un proyecto grande (como un Dataflow), tu código M no es solo una consulta, sino un "documento" que puede tener múltiples partes.

* `section MiSeccion;`
* `shared MiVariable = 1;`
* `MiFuncion = () => ...;`

> No te preocupes por esto para Power BI Desktop, pero es la estructura formal que usa M para organizar archivos `.pq` completos.


