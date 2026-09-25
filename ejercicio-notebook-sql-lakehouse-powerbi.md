# Ejercicio extra: de una base de datos SQL a un informe de Power BI, pasando por un notebook y un lakehouse

> ⚠️ **Este ejercicio NO forma parte del temario oficial del examen DP-600.** El elemento "SQL database in Microsoft Fabric" está explícitamente fuera de alcance del curso oficial (aparece como *"Not Taught"* en el módulo *Choose data stores*). Se ha creado como práctica adicional para reforzar, en un solo flujo de principio a fin, conceptos que sí entran en el examen: notebooks de Spark, tablas Delta en un lakehouse, y modelos semánticos con Direct Lake.

## Qué vas a construir

Un flujo completo de analítica de extremo a extremo, usando **solo recursos dentro de tu propio tenant de Fabric** (nada de servidores públicos externos que puedan no estar disponibles):

```
SQL database en Fabric (datos de ventas)
        │  JDBC + token de Entra ID
        ▼
   Notebook (PySpark)
        │  escribe tabla Delta
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

Entre 45 y 60 minutos, dependiendo de tu familiaridad con Spark y Power BI Desktop.

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

## Paso 3 — Obtener el connection string de la base de datos

1. En la página de la base de datos `AdventureWorksLT`, selecciona el icono de configuración (**⚙️ Settings**) o el menú **"..."**.
2. Busca la sección **Connection strings** (o **Cadenas de conexión**).
3. Copia el **servidor SQL** (tendrá una forma parecida a `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.database.fabric.microsoft.com`).
4. Guárdalo en un bloc de notas — lo necesitarás en el Paso 6.

---

## Paso 4 — Crear el lakehouse de destino

1. Vuelve al workspace y selecciona **+ New item**.
2. Selecciona **Lakehouse**, dale el nombre `lh_ventas` y selecciona **Create**.
3. Espera a que se aprovisione (aparecerán las áreas **Tables** y **Files** vacías).

---

## Paso 5 — Crear el notebook

1. En el workspace, selecciona **+ New item** → **Notebook**.
2. Dale un nombre, por ejemplo `nb_carga_ventas`.
3. En el panel del explorador (a la izquierda del notebook), selecciona **Add data items** o **Lakehouses** → **Add** → elige `lh_ventas` para adjuntarlo como lakehouse por defecto del notebook.

---

## Paso 6 — Conectar por JDBC usando tu identidad de Fabric

En la primera celda del notebook, pega el siguiente código. **No necesitas usuario ni contraseña** — se usa el token de tu propia sesión de Fabric (Entra ID), que ya tiene acceso a la base de datos porque la creaste en el mismo tenant.

```python
# Celda 1 — Obtener un token de autenticación para SQL
token = notebookutils.credentials.getToken("https://database.windows.net/")

servidor = "<PEGA_AQUI_TU_SERVIDOR>.database.fabric.microsoft.com"  # del Paso 3

jdbc_url = (
    f"jdbc:sqlserver://{servidor}:1433;"
    "database=AdventureWorksLT;encrypt=true;trustServerCertificate=false;"
    "hostNameInCertificate=*.database.fabric.microsoft.com;loginTimeout=30"
)
```

```python
# Celda 2 — Leer la tabla de pedidos de venta
df_ventas = (spark.read
    .format("jdbc")
    .option("url", jdbc_url)
    .option("dbtable", "SalesLT.SalesOrderHeader")
    .option("accessToken", token)
    .load())

display(df_ventas)
```

> ⚠️ **Si la celda 2 falla:** revisa primero que el nombre del servidor del Paso 3 esté copiado sin espacios ni comillas de más. Si el error menciona permisos, confirma que tu usuario tiene rol de propietario o colaborador sobre el workspace donde vive la base de datos. Este patrón de conexión no forma parte del temario oficial, así que tómate un momento para probarlo con calma antes de usarlo en directo delante de un grupo.

```python
# Celda 3 — Leer también clientes y productos
df_clientes = (spark.read.format("jdbc")
    .option("url", jdbc_url)
    .option("dbtable", "SalesLT.Customer")
    .option("accessToken", token)
    .load())

df_productos = (spark.read.format("jdbc")
    .option("url", jdbc_url)
    .option("dbtable", "SalesLT.Product")
    .option("accessToken", token)
    .load())
```

---

## Paso 7 — (Opcional) Una pequeña transformación antes de guardar

Aprovecha para practicar lo visto en el módulo de notebooks: une ventas con clientes usando un `join`, y añade una columna calculada.

```python
from pyspark.sql.functions import col

df_ventas_enriquecido = (
    df_ventas
    .join(df_clientes, df_ventas.CustomerID == df_clientes.CustomerID, "left")
    .select(
        df_ventas.SalesOrderID,
        df_ventas.OrderDate,
        df_ventas.SubTotal,
        df_clientes.FirstName,
        df_clientes.LastName
    )
)

display(df_ventas_enriquecido)
```

---

## Paso 8 — Escribir las tablas Delta en el lakehouse

```python
# Celda final — Guardar como tablas Delta gestionadas del lakehouse
df_ventas_enriquecido.write.format("delta").mode("overwrite").saveAsTable("ventas")
df_clientes.write.format("delta").mode("overwrite").saveAsTable("clientes")
df_productos.write.format("delta").mode("overwrite").saveAsTable("productos")
```

Ve al lakehouse `lh_ventas` y actualiza el explorador de tablas (botón de refrescar) — deberías ver `ventas`, `clientes` y `productos` bajo **Tables**.

---

## Paso 9 — Crear el modelo semántico

1. Abre el lakehouse `lh_ventas`.
2. En la barra superior, selecciona **New semantic model** (o usa el modelo semántico por defecto que Fabric ya creó automáticamente junto con el lakehouse).
3. Añade las tres tablas: `ventas`, `clientes`, `productos`.
4. En la vista de modelo, crea la relación `ventas.CustomerID` → `clientes.CustomerID` (uno a muchos).
5. (Opcional) Crea una medida sencilla en DAX:

   ```dax
   Total Ventas = SUM(ventas[SubTotal])
   ```

6. Guarda el modelo semántico.

> 💡 Este modelo usa **Direct Lake** por defecto — lee directamente las tablas Delta del lakehouse sin necesidad de programar ningún refresco.

---

## Paso 10 — Abrir en Power BI Desktop

1. En Fabric, sobre el modelo semántico, selecciona **"Open in Power BI Desktop live connect"** (o similar, según tu versión).
2. Se abrirá Power BI Desktop conectado en vivo a tu modelo semántico.
3. Crea una visualización rápida: un gráfico de columnas con `Total Ventas` por `LastName`, por ejemplo.
4. Guarda el informe (`.pbix`) si quieres conservarlo.

---

## Resumen de lo que has practicado

- Crear una base de datos SQL operacional dentro de Fabric, sin infraestructura externa.
- Conectar un notebook Spark a una fuente SQL vía JDBC, autenticando con tu propia identidad de Fabric (sin usuario/contraseña sueltos).
- Transformar datos con PySpark (`join`, selección de columnas).
- Escribir tablas Delta gestionadas en un lakehouse.
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
| `Login failed` al ejecutar la celda del JDBC | El token ha caducado o el servidor está mal copiado | Vuelve a ejecutar la celda del token; revisa que el nombre del servidor no tenga espacios |
| No aparece la tarjeta "Sample data" al crear la base de datos | La base de datos aún se está aprovisionando | Espera unos segundos y refresca la página |
| Las tablas no aparecen en el lakehouse tras escribirlas | El explorador de tablas no se ha refrescado | Usa el botón de refrescar (⟳) en el panel de **Tables** |
| El modelo semántico no refleja los últimos datos | Direct Lake cachea agresivamente en algunos casos | En el modelo semántico, usa **Refresh** manual |

## Referencias

- Laboratorio oficial en el que se basa la parte de la base de datos: [Work with SQL Database in Microsoft Fabric](https://microsoftlearning.github.io/mslearn-fabric/Instructions/Labs/20-work-with-database.html)
- [Documentación de Fabric notebooks](https://learn.microsoft.com/fabric/data-engineering/how-to-use-notebook)
- [SQL database en Microsoft Fabric](https://learn.microsoft.com/fabric/database/sql/overview)
