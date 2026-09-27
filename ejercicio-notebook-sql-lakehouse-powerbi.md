# Ejercicio extra: de una base de datos SQL a un informe de Power BI, pasando por un notebook y un lakehouse

> ⚠️ **Este ejercicio NO forma parte del temario oficial del examen DP-600.** El elemento "SQL database in Microsoft Fabric" está explícitamente fuera de alcance del curso oficial (aparece como *"Not Taught"* en el módulo *Choose data stores*). Se ha creado como práctica adicional para reforzar, en un solo flujo de principio a fin, conceptos que sí entran en el examen: notebooks, tablas Delta en un lakehouse, y modelos semánticos con Direct Lake.

> 📝 **Nota de la versión 2:** la primera versión de este ejercicio usaba una conexión JDBC clásica (`spark.read.format("jdbc")` + `notebookutils.credentials.getToken`) para leer la SQL database desde el notebook. En la práctica, esa conexión **falla** con `Login failed` — es un problema conocido y reportado en la comunidad, que Microsoft tiene marcado como *"Planned"* (no soportado todavía). Esta versión sustituye ese paso por el comando mágico **`%%tsql`**, que sí funciona, y añade las correcciones necesarias en la escritura del Delta. Todo lo indicado a continuación está verificado contra un notebook que se ha ejecutado con éxito de principio a fin.

## Qué vas a construir

Un flujo completo de analítica de extremo a extremo, usando **solo recursos dentro de tu propio tenant de Fabric** (nada de servidores públicos externos que puedan no estar disponibles):

```
SQL database en Fabric (datos de ventas)
        │  comando mágico %%tsql
        ▼
   Notebook (lenguaje Python, no PySpark)
        │  pandas → Polars → write_delta (ruta ABFS + token)
        ▼
     Lakehouse
        │
        ▼
  Modelo semántico (Direct Lake)
        │
        ▼
    Informe en Power BI
```

## Duración estimada

Entre 45 y 60 minutos, dependiendo de tu familiaridad con notebooks y Power BI Desktop.

## Prerrequisitos

- [ ] Acceso a una capacidad de Fabric (Trial, Premium o Fabric). Si no tienes una, puedes activar la [prueba gratuita de Fabric](https://aka.ms/fabrictrial).
- [ ] Permisos para crear workspaces en tu tenant.
- [ ] [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=58494) instalado (solo hace falta para el último paso).
- [ ] No necesitas ninguna base de datos externa, servidor SQL propio, ni credenciales adicionales — todo se crea dentro de Fabric.

---

## Paso 1 — Crear un workspace

1. Ve a [https://app.fabric.microsoft.com](https://app.fabric.microsoft.com) e inicia sesión con tu cuenta de Fabric.
2. En el menú de la izquierda, selecciona **Workspaces** → **New workspace**.
3. Dale un nombre (por ejemplo, `lab-notebook-sql`) y selecciona un modo de licencia que incluya capacidad Fabric (*Trial*, *Premium* o *Fabric*).
4. Selecciona **Apply**. El workspace se abrirá vacío.

---

## Paso 2 — Crear la SQL database con datos de ejemplo

1. En el workspace, selecciona **+ New item** (o **Create** en el menú lateral).
2. En la sección *Databases*, selecciona **SQL database**.
3. Ponle el nombre `AdventureWorksLT` y selecciona **Create**.
4. Cuando la base de datos termine de aprovisionarse, verás una tarjeta llamada **Sample data** — selecciónala.
5. Espera aproximadamente un minuto: la base de datos se rellenará con el esquema clásico `SalesLT` (productos, clientes, pedidos de venta, direcciones...).

> 💡 **Qué es esto realmente:** el esquema `SalesLT` es el conocido *AdventureWorksLT*, la base de ejemplo que Microsoft usa en decenas de tutoriales de SQL Server y Power BI desde hace años. Aquí la tienes lista para usar, sin instalar nada.

### (Opcional) Explora los datos con T-SQL

Antes de pasar al notebook, puedes echar un vistazo con una consulta rápida. En **Home** → **New query**, pega y ejecuta:

```sql
-- Esto es un comentario en T-SQL (dos guiones)
SELECT
    p.Name AS ProductName,
    pc.Name AS CategoryName,
    p.ListPrice
FROM
    SalesLT.Product p
INNER JOIN
    SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
ORDER BY
    p.ListPrice DESC;
```

---

## Paso 3 — Crear el lakehouse de destino

1. Vuelve al workspace y selecciona **+ New item**.
2. Selecciona **Lakehouse**, dale el nombre `lh_ventas` y selecciona **Create**.
3. Espera a que se aprovisione (aparecerán las áreas **Tables** y **Files** vacías).

---

## Paso 4 — Crear el notebook

1. En el workspace, selecciona **+ New item** → **Notebook**.
2. Dale un nombre, por ejemplo `nb_carga_ventas`.
3. **Importante:** este notebook necesita que su lenguaje por defecto sea **Python** (no PySpark). Compruébalo/cámbialo en el desplegable de lenguaje de la celda — si ves `PySpark (Python)`, cámbialo a `Python`. El comando mágico `%%tsql` del siguiente paso solo funciona en notebooks de Python.
4. No hace falta adjuntar el lakehouse todavía como valor por defecto: la escritura del Paso 7 usa una ruta completa (ABFS), así que basta con tenerlo creado.

---

## Paso 5 — Consultar la SQL database con el comando mágico `%%tsql`

En lugar de una conexión JDBC manual, Fabric ofrece un comando mágico que ejecuta T-SQL directamente contra tu SQL database y vuelca el resultado en un DataFrame de pandas.

```python
# Celda 1 - Consultar la SQL database con el comando mágico oficial
%%tsql -artifact AdventureWorksLT -type SQLDatabase -bind df_ventas
SELECT * FROM SalesLT.SalesOrderHeader;
```

> ⚠️ **Sintaxis:** `-artifact` es el nombre de tu SQL database (aquí `AdventureWorksLT`, del Paso 2), `-type SQLDatabase` indica el tipo de artefacto, y `-bind` es el nombre de la variable de Python donde quedará el resultado como DataFrame de pandas. La consulta T-SQL va en la línea siguiente.

```python
# Celda 2 — Comprobar qué tipo de objeto te ha devuelto
print(type(df_ventas))
df_ventas.head()
```

Repite el patrón para traer también clientes y productos:

```python
# Celda 3 y 4 — Traer también clientes y productos
%%tsql -artifact AdventureWorksLT -type SQLDatabase -bind df_clientes
SELECT * FROM SalesLT.Customer;
```

```python
%%tsql -artifact AdventureWorksLT -type SQLDatabase -bind df_productos
SELECT * FROM SalesLT.Product;
```

> ⚠️ **Si la consulta sobre `SalesLT.Product` falla** con un error relacionado con codificación/UTF-8 al leer alguna columna de texto larga, puedes omitir esa tabla sin problema — no es necesaria para el resto del ejercicio, que se centra en `ventas` y `clientes`.

---

## Paso 6 — (Opcional) Una pequeña transformación antes de guardar

Aprovecha para practicar una unión de tablas con pandas:

```python
# Celda 5 -> unir ventas con clientes
df_enriquecido = df_ventas.merge(
    df_clientes,
    on="CustomerID",
    how="left",
    suffixes=("", "_cliente")
)
df_enriquecido.head()
```

Si vas a escribir en Delta un DataFrame que tenga columnas de texto totalmente vacías (por ejemplo `MiddleName` o `Suffix` en `SalesLT.Customer`), conviene forzarlas a tipo `string` explícito antes de escribir — si no, Delta puede rechazar la escritura (ver la tabla de solución de problemas):

```python
# Convierte todas las columnas de texto (incluidas las vacías) a un tipo string explícito
cols_texto = df_enriquecido.select_dtypes(include="object").columns
df_enriquecido[cols_texto] = df_enriquecido[cols_texto].astype("string")
```

> 💡 Este paso es opcional para el flujo mínimo (el Paso 7 escribe `df_ventas` tal cual), pero es imprescindible si decides guardar `df_enriquecido` o cualquier DataFrame con columnas de texto vacías.

---

## Paso 7 — Escribir la tabla Delta en el lakehouse

Aquí está el cambio más importante respecto al planteamiento original: **no se escribe con `saveAsTable` de Spark**, sino con **Polars**, apuntando a una **ruta ABFS completa** dentro de OneLake (una ruta relativa tipo `/lakehouse/default/Tables/...` puede fallar al no poder completar el "rename" atómico que exige Delta).

```python
# Construir la ruta de OneLake y las credenciales de escritura
workspace = "<NOMBRE_DE_TU_WORKSPACE>"      # el workspace del Paso 1
lakehouse = "lh_ventas.Lakehouse"           # el lakehouse del Paso 3 (con el sufijo .Lakehouse)

path = f"abfss://{workspace}@onelake.dfs.fabric.microsoft.com/{lakehouse}/Tables/ventas"

storage_options = {
    "bearer_token": notebookutils.credentials.getToken("storage"),
    "use_fabric_endpoint": "true",
}
```

```python
import polars as pl

pl_df = pl.from_pandas(df_ventas)
pl_df.write_delta(path, mode="overwrite", storage_options=storage_options)
```

> 💡 Repite el mismo patrón (cambiando el nombre de tabla en la ruta y el DataFrame de origen) para guardar `df_clientes` y, si la obtuviste, `df_productos`. Si prefieres guardar la versión unida y con columnas de texto corregidas, usa `df_enriquecido` en lugar de `df_ventas`.

Ve al lakehouse `lh_ventas` y actualiza el explorador de tablas (botón de refrescar) — deberías ver `ventas` (y el resto de tablas que hayas escrito) bajo **Tables**.

---

## Paso 8 — Crear el modelo semántico

1. Abre el lakehouse `lh_ventas`.
2. En la barra superior, selecciona **New semantic model** (o usa el modelo semántico por defecto que Fabric ya creó automáticamente junto con el lakehouse).
3. Añade la tabla `ventas` (y `clientes`/`productos` si las escribiste).
4. Si escribiste tablas separadas, en la vista de modelo crea la relación `ventas.CustomerID` → `clientes.CustomerID` (uno a muchos).
5. (Opcional) Crea una medida sencilla en DAX:

   ```dax
   Total Ventas = SUM(ventas[SubTotal])
   ```

6. Guarda el modelo semántico.

> 💡 Este modelo usa **Direct Lake** por defecto — lee directamente las tablas Delta del lakehouse sin necesidad de programar ningún refresco.

---

## Paso 9 — Abrir en Power BI Desktop

1. En Fabric, sobre el modelo semántico, selecciona **"Open in Power BI Desktop live connect"** (o similar, según tu versión).
2. Se abrirá Power BI Desktop conectado en vivo a tu modelo semántico.
3. Crea una visualización rápida: un gráfico de columnas con `Total Ventas` por cliente, por ejemplo.
4. Guarda el informe (`.pbix`) si quieres conservarlo.

---

## Resumen de lo que has practicado

- Crear una base de datos SQL operacional dentro de Fabric, sin infraestructura externa.
- Consultar una SQL database de Fabric desde un notebook usando el comando mágico `%%tsql`, sin gestionar tokens ni cadenas de conexión a mano.
- Transformar datos con pandas (`merge`, casteo de tipos).
- Escribir tablas Delta en un lakehouse usando Polars y una ruta ABFS explícita con token de OneLake.
- Construir un modelo semántico con relaciones y una medida DAX sobre Direct Lake.
- Consumir ese modelo en un informe de Power BI.

## Limpieza de recursos

Si has terminado de practicar y no quieres conservar estos elementos:

1. Ve al workspace `lab-notebook-sql`.
2. Abre **Workspace settings** desde el menú **"..."**.
3. En la sección **General**, selecciona **Remove this workspace**.

---

## Solución de problemas comunes

| Problema | Causa probable | Solución |
|---|---|---|
| `Py4JJavaError` / `Login failed` al leer con `spark.read.format("jdbc")` | La SQL database de Fabric no admite (todavía) tokens de `notebookutils.credentials.getToken` para JDBC — es un problema conocido, marcado por Microsoft como *"Planned"* | Sustituye la conexión JDBC por el comando mágico `%%tsql -artifact <nombre> -type SQLDatabase -bind <variable>` |
| `UsageError: Cell magic '%%tsql' not found` | El notebook está en modo PySpark, no en Python | Cambia el lenguaje del notebook a **Python** en el desplegable de lenguaje |
| `Schema error: Invalid data type for Delta Lake: Null` al hacer `write_delta` | Una columna del DataFrame es completamente nula (por ejemplo `MiddleName`, `Suffix`) y Delta no sabe qué tipo asignarle | Antes de escribir, convierte las columnas de tipo `object` a `string` explícito: `df[cols].astype("string")` |
| `Generic LocalFileSystem error ↳ Unable to rename file ↳ Operation not permitted (os error 1)` | Se ha usado una ruta relativa tipo `/lakehouse/default/Tables/...`, que no soporta el rename atómico que necesita Delta | Usa la ruta ABFS completa (`abfss://{workspace}@onelake.dfs.fabric.microsoft.com/{lakehouse}.Lakehouse/Tables/{tabla}`) junto con `storage_options` que incluya el `bearer_token` de `notebookutils.credentials.getToken("storage")` y `"use_fabric_endpoint": "true"` |
| No aparece la tarjeta "Sample data" al crear la base de datos | La base de datos aún se está aprovisionando | Espera unos segundos y refresca la página |
| Las tablas no aparecen en el lakehouse tras escribirlas | El explorador de tablas no se ha refrescado | Usa el botón de refrescar (⟳) en el panel de **Tables** |
| El modelo semántico no refleja los últimos datos | Direct Lake cachea agresivamente en algunos casos | En el modelo semántico, usa **Refresh** manual |

## Referencias

- Laboratorio oficial en el que se basa la parte de la base de datos: [Work with SQL Database in Microsoft Fabric](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/20-work-with-database.html)
- [Documentación de Fabric notebooks](https://learn.microsoft.com/fabric/data-engineering/how-to-use-notebook)
- [SQL database en Microsoft Fabric](https://learn.microsoft.com/fabric/database/sql/overview)
- [Comando mágico %%tsql en notebooks de Fabric](https://learn.microsoft.com/fabric/database/sql/query-with-python-notebooks)
