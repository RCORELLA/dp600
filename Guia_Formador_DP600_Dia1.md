# Guía del Formador — DP-600T00A · Día 1
## Implement analytics solutions using Microsoft Fabric

Basada en las 58 diapositivas y las notas del orador del deck `DP-600T00A-ENU-PowerPoint_01.pptx`. El propio deck ya trae una "Guía del formador (ES)" incrustada en las notas de las diapositivas de apertura de cada módulo (3, 13, 23, 35, 47); este documento la recoge, la completa slide a slide y añade una valoración de tiempos y mejoras.

---

## 0. Resumen ejecutivo: ¿llega el temario a 5 horas?

El propio material trae, en las notas de cada slide de título de sección, una estimación oficial de duración (incluye demo + ejercicio de 30 min):

| Módulo | Contenido | Estimación oficial |
|---|---|---|
| 1 | Explorar analítica de extremo a extremo con Fabric | 40–45 min |
| 2 | Descubrir y conectar datos en OneLake | 60–70 min |
| 3 | Lakehouses en Microsoft Fabric | 75–80 min |
| 4 | Data warehouses en Microsoft Fabric | 70–80 min |
| 5 | Real-Time Intelligence | 60–65 min |
| **Total (sin descansos)** | | **305–340 min → 5 h 05 min – 5 h 40 min** |

**Respuesta corta: no os falta temario — probablemente os sobra.** Incluso en el escenario más optimista (305 min) ya se come toda tu ventana de 5 horas (300 min) sin dejar ni un minuto para descansos, arranque de sala, dudas fuera de guion o retrasos en los laboratorios (que casi siempre tardan más de los 30 min "oficiales" la primera vez que un grupo los hace). En el escenario realista (320–340 min) más un descanso de café (15 min) y los inevitables desajustes, estáis hablando de **5 h 30 min – 6 h**, es decir, entre 30 y 60 minutos por encima de lo que tenéis.

**Recomendación práctica:**
- No añadas contenido nuevo. El riesgo real es no llegar al Módulo 5, no que sobre tiempo.
- Ten preparado un "plan B" de recorte, priorizando así (de lo primero que sacrificarías a lo último):
  1. Recorta las demos a lo imprescindible (usa el "Click Path" de cada módulo como guion cerrado, sin improvisar).
  2. En los Knowledge Check (slides 11, 21, 33, 45, 56), no le dediques más de 3 min a cada uno; si el grupo va bien, resuélvelos en voz alta sin votación formal.
  3. Si vas muy justo de tiempo al llegar al Módulo 5, puedes convertir el ejercicio de Real-Time Intelligence (slide 55) en una demo guiada por ti en vez de práctica individual — es el módulo diseñado para "quedar corto" si hace falta, porque el temario de Real-Time Intelligence se retoma y profundiza el resto del curso.
  4. Ten localizada de antemano la sección "Not Taught" de cada módulo (están listadas más abajo) para poder decir con seguridad "esto lo dejamos para Microsoft Learn" si alguien pregunta y vas con prisa.
- Considera avisar a los asistentes al empezar de que el ritmo va a ser vivo y que los descansos serán cortos (5–10 min), no de 15–20.

---

## 1. Agenda del día (slide 2)

1. Explorar analítica de extremo a extremo con Microsoft Fabric
2. Descubrir y conectar datos en OneLake
3. Primeros pasos con lakehouses
4. Primeros pasos con data warehouses
5. Primeros pasos con Real-Time Intelligence

**Tema del día:** presentar la plataforma Fabric y sus tres almacenes analíticos. **Resultado esperado al final del día:** el alumno entiende el panorama de la plataforma y ha trabajado, individualmente, dentro de un lakehouse, un warehouse y un eventhouse.

**Mensaje de apertura sugerido (hilo conductor de todo el día):** *una sola copia del dato*. Lakehouse, warehouse, eventhouse y Direct Lake escriben y leen todos de OneLake en formato Delta-Parquet. Si el grupo se lleva solo esa idea, el resto de la jornada encaja solo.

---

## MÓDULO 1 — Explorar analítica de extremo a extremo con Fabric
**Slides 3–12 · 40–45 min · Learn:** introduction-end-analytics-use-microsoft-fabric

### Lo que se enseña vs. lo que NO se enseña
- ✅ Fabric como plataforma SaaS unificada · OneLake · Workloads (qué hace cada uno y quién lo usa) · Equipo de datos colaborativo · Cómo empezar (habilitar Fabric, workspaces, catálogo) · Capacidades de IA (Copilot, agentes de datos, Fabric IQ)
- ❌ No entrar en: documentación de Admin API/SDK, configuración de Spark, integración con Git

### Recorrido slide a slide

| # | Título | Punto clave para el aula |
|---|---|---|
| 4 | Learning objectives | Explicar qué es Fabric y por qué unifica la analítica; identificar workloads y su rol; describir cómo OneLake habilita la IA. Apertura del curso — sin ejercicio, la demo hace de toma de contacto. |
| 5 | El problema — analítica fragmentada | Un ingeniero, un analista y un científico de datos usan 3 herramientas distintas → 3 copias del mismo dato → nadie sabe cuál es la buena el lunes. Pregúntale a la sala cuántas herramientas usan hoy. |
| 6 | La solución — plataforma SaaS unificada | Slide "revelación", deliberadamente breve — no te pares aquí, la arquitectura viene en la siguiente. Mensaje único: Fabric sustituye la dispersión de herramientas por un entorno único. |
| 7 | Arquitectura de Microsoft Fabric | Diagrama en capas: workloads arriba, motores de cómputo en medio, OneLake abajo. Recorre el diagrama de izquierda a derecha nombrando cada workload y su usuario típico. No profundices en ninguno — cada uno tiene su propio módulo más adelante. |
| 8 | Colaboración de equipos de datos en Fabric | Elimina los silos por rol: ingenieros ingieren, analistas informan, científicos modelan — todos sobre el mismo dato. El "analytics engineer" es el rol puente entre ingeniería y análisis. |
| 9 | Capacidades de IA en Fabric | El dato gobernado en OneLake está automáticamente disponible para la IA — no hay "pipeline de IA" aparte. Fabric IQ (ontologías de negocio), agentes de datos (lenguaje natural), Copilot (en todos los workloads). |
| 10 | Demo: Navegar por Microsoft Fabric | Portal → Admin portal/Tenant settings → workspace (ajustes, modo de licencia) → catálogo OneLake (filtros, panel de detalle) → selector de workloads (+ New) → opcional: panel de Copilot en un notebook. Objetivo: "he visto dónde está todo", no dominar la interfaz. |
| 11 | Knowledge check | 3 preguntas — ver respuestas abajo |
| 12 | Resumen de sección | SaaS unificado → OneLake (una copia, formato abierto) → Workloads por rol → Dato gobernado habilita la IA |

### Conceptos clave y definiciones (Módulo 1)

- **Microsoft Fabric:** plataforma SaaS unificada de análisis. La palabra clave es *SaaS*: no se provisionan clústeres ni cadenas de conexión (a diferencia de Azure Synapse, que es PaaS y el cliente ensambla las piezas).
- **OneLake:** un único data lake por tenant, creado automáticamente al habilitar Fabric — no es opcional ni se puede eliminar. Analogía útil: *OneLake es a los datos lo que OneDrive es a los documentos*.
- **Workloads (7 núcleos):**
  | Workload | Para qué sirve | Perfil que lo usa |
  |---|---|---|
  | Data Factory | Ingesta y orquestación de pipelines | Integradores de datos |
  | Data Engineering | Lakehouses, transformación con Spark | Ingenieros de datos |
  | Data Warehouse | Analítica T-SQL sobre datos estructurados | Analistas SQL |
  | Data Science | Modelos ML, experimentos | Científicos de datos |
  | Real-Time Intelligence | Eventos y KQL | Operaciones / IoT |
  | Power BI | Modelos semánticos, informes | Analistas de negocio |
  | Fabric IQ (preview) | Ontologías de negocio para que los agentes de IA entiendan tu organización | Todos, vía IA |
- **Capacidad:** unidad que se factura, medida en CU (Capacity Units), SKU de F2 a F2048. Se paga por capacidad compartida, no por servicio suelto. *Smoothing* reparte los picos de consumo; *bursting* permite superar el límite momentáneamente.
- **Jerarquía a dibujar en la pizarra (se usa los dos días):** `Tenant → Capacidad → Workspace → Item`.
- **Copilot:** disponible en cada workload; requiere capacidad F64+ (o P1+) y habilitación por el administrador del tenant. Su calidad depende de los metadatos: nombres descriptivos, relaciones bien definidas y descripciones mejoran sus respuestas.

### Ejemplo de aplicación
Una aseguradora tiene hoy un ETL en una herramienta, los informes en Power BI Desktop sin gobierno central y un notebook de ML suelto en el portátil de un científico de datos. Con Fabric, los tres trabajan sobre las mismas tablas Delta en OneLake: el ingeniero las prepara, el analista construye el informe con Direct Lake, y el modelo de riesgo del científico de datos lee la misma tabla — sin exportar, sin duplicar, y con Copilot generando sugerencias en los tres workloads porque el dato está bien descrito.

### Knowledge check — respuestas (slide 11)
1. **Beneficio clave de Fabric:** proporciona un entorno único e integrado para colaborar en proyectos de datos.
2. **Formato de almacenamiento por defecto de OneLake:** Delta-Parquet.
3. **Por qué el modelo unificado de OneLake importa para la IA:** Copilot y los agentes de datos acceden al mismo dato gobernado sin pipelines de preparación separados.

### 💡 Detectado al revisar el deck
- **Slide 8** es la más densa del módulo en su volcado de texto (listas de workloads repetidas por columna de rol) — en pantalla es un diagrama de columnas, pero leído como texto es confuso. **Sugerencia:** no lo narres slide por slide; señala directamente las columnas del diagrama en pantalla ("aquí los ingenieros, aquí los analistas...") en vez de enumerar los workloads en voz alta.
- Las slides 3, 13, 23, 35 y 47 (las de título de sección) no tienen contenido visible para el alumno — son solo para ti. Tenlas como tu "chuleta" abierta en el portátil/notas del presentador, no proyectadas.

---

## MÓDULO 2 — Descubrir y conectar datos en OneLake
**Slides 13–22 · 60–70 min · Learn:** discover-data-onelake

### Lo que se enseña vs. lo que NO se enseña
- ✅ OneLake como almacenamiento unificado a nivel de tenant · catálogo de OneLake (búsqueda, metadatos, endorsement, modelos semánticos) · Shortcuts (referencia sin copia, cross-workspace y externos) · Real-Time hub (descubrir streaming)
- ❌ No entrar en: métodos de ingesta (mirroring, pipelines, dataflows), configuración avanzada de eventstream, detalles de Copilot/IA, especificidades de Delta Lake

### Recorrido slide a slide

| # | Título | Punto clave para el aula |
|---|---|---|
| 14 | Learning objectives | Explicar cómo OneLake da almacenamiento unificado; navegar el catálogo; crear shortcuts; descubrir streaming en Real-Time hub. Es sobre *descubrimiento y conexión*, no ingesta ni transformación. |
| 15 | ¿Qué es OneLake? | "Un tenant, un lago, una copia." Estructura: `Tenant (OneLake) → Workspace → Item → Tables/ (Delta) y Files/ (cualquier formato)`. |
| 16 | Catálogo de OneLake | Punto único para descubrir datos del tenant: búsqueda por nombre/keyword/tag, filtros por tipo/workspace/dominio, insignias de confianza. Respeta permisos — cada usuario ve un catálogo personalizado. |
| 17 | Shortcuts: acceso sin copiar | **El concepto más importante del módulo.** Diagrama con varios orígenes (otro lakehouse, ADLS Gen2, AWS S3/GCS) apuntando a "tu lakehouse" con el rótulo "zero data movement". |
| 18 | Real-Time Hub: datos en streaming | El equivalente al catálogo pero para datos en movimiento (eventstreams y tablas KQL). Se profundiza en el Módulo 5. |
| 19 | Demo: explorar catálogo y crear un shortcut | Catálogo (búsqueda, filtros, metadatos) → lakehouse → Get data → New shortcut → OneLake → elegir tablas → verlo en el explorador → SQL analytics endpoint con un SELECT de comprobación → opcional: vistazo al Real-Time hub. |
| 20 | Ejercicio: Discover data in OneLake | 30 min — crear lakehouse, poblarlo, descubrir en catálogo, crear shortcut, consultar vía SQL endpoint. Lab: `25-discover-onelake.html` |
| 21 | Knowledge check | 3 preguntas — ver respuestas abajo |
| 22 | Resumen de sección | OneLake = un lago por tenant, una copia · catálogo descubre datos en reposo con metadatos y endorsement · shortcuts referencian sin duplicar · Real-Time hub descubre streaming |

### Conceptos clave y definiciones (Módulo 2)

- **Delta Lake:** formato nativo de tabla en Fabric. Sobre Parquet a secas, añade transacciones ACID, *time travel* (consultar una versión anterior), evolución de esquema y `MERGE`/`UPDATE`/`DELETE`. Es lo que permite que Power BI lea en modo Direct Lake.
- **Catálogo de OneLake:** metadatos + propietario + linaje + fecha de refresco. Insignias de confianza (endorsement):
  | Insignia | Quién la otorga | Significado |
  |---|---|---|
  | Promoted | El propietario del elemento | Recomendación informal |
  | Certified | Un rol autorizado por el administrador | Señal formal de confianza |
  | Master Data | — | Marca como dato de referencia autoritativo |
  - Las etiquetas de confidencialidad de Purview viajan con el dato, incluso al exportar a Excel.
- **Shortcut:** una **referencia** a datos que viven en otro sitio, nunca una copia. Orígenes: otro lakehouse, otro workspace, ADLS Gen2, Amazon S3, Google Cloud Storage, bases con mirroring.
  - Los datos **no** se duplican: se consultan en origen.
  - Los permisos se evalúan en el origen — un shortcut no concede acceso por sí mismo.
  - Cualquier motor puede leerlo: Spark, SQL endpoint, Direct Lake.
  - La latencia depende del origen: un shortcut a S3 no rinde como una tabla local.
- **Real-Time Hub:** catálogo para eventstreams y tablas KQL. Orígenes típicos: IoT, CDC de bases de datos, Kafka, clickstream, eventos de Azure.

### Ejemplo de aplicación
Un equipo de analítica necesita las tablas de ventas que ya mantiene el equipo de ingeniería en otro workspace. En vez de pedir una exportación o montar un pipeline de copia, crean un **shortcut** desde su lakehouse al lakehouse de ingeniería: las tablas aparecen bajo `Tables/` como si fueran propias, se consultan con SQL o Spark exactamente igual, y cuando ingeniería actualiza el dato de origen, el cambio se ve al instante — sin refresco, sin duplicar almacenamiento. Es también el mecanismo detrás de la arquitectura medallón: un lakehouse *silver* usa un shortcut al *bronze* en lugar de copiar los datos crudos.

### Knowledge check — respuestas (slide 21)
1. **Beneficio principal de OneLake como lago a nivel de tenant:** todos los workloads leen y escriben en el mismo almacenamiento, eliminando silos.
2. **Acceso T-SQL de solo lectura a tablas de otro equipo sin copiar:** usar el SQL analytics endpoint del lakehouse.
3. **Propósito de los shortcuts:** referenciar datos de otros workspaces o ubicaciones externas sin copiarlos.

### 💡 Detectado al revisar el deck
- **Slide 17** (Shortcuts), igual que la slide 8 del Módulo 1, vuelca mucho texto de un diagrama (varios orígenes, flechas, "zero data movement"). Dado que el propio guion la marca como "el concepto más importante del módulo", merece la pena **no leerla**, sino dibujar tú mismo en la pizarra el esquema `Origen externo → shortcut → tu lakehouse (sin copia)` mientras hablas — se fija mucho mejor que el texto de la diapositiva.
- Fíjate en el error de comprensión que trae el propio guion: mucha gente cree que *"si creo un shortcut, mi equipo ya puede ver los datos"* — no es así, los permisos siguen siendo los del origen. Merece la pena remarcarlo explícitamente en voz alta, no solo dejarlo en tus notas.

---

## MÓDULO 3 — Primeros pasos con lakehouses
**Slides 23–34 · 75–80 min (incluye 30 min de ejercicio) · Learn:** get-started-lakehouses

### Lo que se enseña vs. lo que NO se enseña
- ✅ Qué es un lakehouse · arquitectura (Tables/Delta vs. Files, capacidades de Delta) · métodos de ingesta · shortcuts · patrón de transformación Files→Tables · consulta (SQL endpoint y Spark) · integración con Power BI (Direct Lake)
- ❌ No entrar en: seguridad detallada (RLS, CLS, a nivel de esquema), gobierno con Purview, detalles de Copilot/IQ, namespace cross-workspace, shortcuts de esquema

### Recorrido slide a slide

| # | Título | Punto clave para el aula |
|---|---|---|
| 24 | Learning objectives | Describir capacidades del lakehouse; crear uno e ingerir datos; consultarlo con SQL y Spark; conectar Power BI vía Direct Lake. |
| 25 | ¿Qué es un lakehouse? | Combina la flexibilidad de un data lake con la analítica estructurada de un warehouse. Al crearlo, Fabric aprovisiona automáticamente 3 elementos: el lakehouse, un modelo semántico por defecto y un SQL analytics endpoint. |
| 26 | Arquitectura del lakehouse | `Files/` = crudo/semiestructurado, cualquier formato, zona de aterrizaje. `Tables/` = formato Delta, estructurado, consultable por SQL, esquema forzado. Capacidades de Delta: actualizaciones eficientes, time travel, transacciones ACID, esquema forzado. |
| 27 | Ingerir datos en un lakehouse | Upload (arrastrar y soltar) · Load to Table (sin código) · Dataflows Gen2 (Power Query) · Notebooks (PySpark/SQL, control total) · Pipelines (ETL orquestado desde 100+ fuentes). |
| 28 | Transformar datos en un lakehouse | Patrón: `Raw (Files) → Transform (notebook/dataflow) → Delta tables`. Notebooks = ingenieros; Dataflows Gen2 = analistas/citizen developers; Pipelines = orquestación visual. |
| 29 | Consultar datos: SQL y Spark | SQL analytics endpoint (auto-creado, T-SQL, **solo lectura**, para analistas) vs. Spark notebooks (lectura+escritura, PySpark/Spark SQL/Scala, para ingenieros). **Ambos consultan las mismas tablas Delta — sin duplicación.** |
| 30 | Analizar con Power BI y Direct Lake | Direct Lake lee Parquet directamente en memoria del motor VertiPaq — sin copia, sin refresco programado. Combina la velocidad de Import con la frescura de DirectQuery. |
| 31 | Demo: crear y explorar un lakehouse | Crear lakehouse → señalar los 3 elementos creados → subir CSV a Files → Load to Table → SQL analytics endpoint con SELECT → opcional: crear un shortcut. |
| 32 | Ejercicio: Create and query a lakehouse | 30 min. Lab: `01-lakehouse.html` |
| 33 | Knowledge check | 3 preguntas — ver respuestas abajo |
| 34 | Resumen de sección | Lakehouse = flexibilidad de lake + analítica de warehouse · Tables/Files · múltiples métodos de ingesta · shortcuts · SQL endpoint + Spark · Direct Lake para informes |

### Conceptos clave y definiciones (Módulo 3)

- **Lakehouse:** combina la flexibilidad de almacenamiento de un data lake (ficheros sin esquema, sin transacciones, motor Spark) con las capacidades de un warehouse (esquema relacional, transacciones, motor SQL). Un lakehouse = ficheros + tablas Delta + transacciones + Spark **y** SQL a la vez.
- **Anatomía al crear un lakehouse (Fabric genera 3 cosas):**
  1. El lakehouse en sí (áreas `Tables/` y `Files/`)
  2. Un **SQL analytics endpoint de solo lectura** sobre las tablas Delta
  3. Un modelo semántico predeterminado para Power BI
  > ⚠️ **Punto que más se pregunta en el examen:** el endpoint SQL del lakehouse es **solo lectura**. Para escribir con T-SQL hace falta un **warehouse**.
- **Métodos de ingesta:** carga manual (pruebas) · pipelines de Data Factory (orquestación a escala) · Dataflows Gen2 (low-code, Power Query) · notebooks PySpark (lógica compleja) · shortcuts (cuando no hay que ingerir nada) · mirroring (réplica continua desde bases operacionales). Regla práctica: **si los datos ya están accesibles, un shortcut gana a una copia.**
- **Arquitectura medallón** (patrón de diseño, no una función de Fabric):
  | Capa | Contenido | Dónde vive |
  |---|---|---|
  | Bronze | Dato crudo tal como llega | Normalmente en `Files/` |
  | Silver | Limpio, deduplicado, tipado, conformado | Tablas Delta |
  | Gold | Agregado y modelado en estrella para consumo de negocio | Tablas Delta |
- **SQL endpoint vs. Spark notebooks:**
  | | SQL analytics endpoint | Spark notebooks |
  |---|---|---|
  | Se crea | Automáticamente con el lakehouse | A mano, en el workspace |
  | Lenguaje | T-SQL | Spark SQL / PySpark / Scala / R |
  | Escritura | No (solo lectura) | Sí |
  | Perfil | Analistas, BI | Ingenieros de datos, ciencia de datos |
- **Direct Lake — modos de almacenamiento en Power BI (concepto clave del curso):**
  | Modo | Cómo funciona | Rendimiento | Frescura |
  |---|---|---|---|
  | Import | Copia el modelo en memoria | Máximo | Depende del último refresco |
  | DirectQuery | El dato vive en el origen, se consulta en vivo | El del origen (más lento) | Tiempo real |
  | **Direct Lake** | Lee Parquet directamente en OneLake, en memoria del motor VertiPaq | Cercano a Import | Casi tiempo real |
  - Requisito: datos en OneLake en formato Delta.
  - Si una consulta excede los límites del modo (p.ej. el *guardrail* de tamaño de la SKU), hace *fallback* a DirectQuery: más lento, pero sigue respondiendo.
  - Mantenimiento: muchos ficheros pequeños degradan el rendimiento — V-Order, `OPTIMIZE` y `VACUUM` forman parte del trabajo.

### Ejemplo de aplicación
Un equipo de logística recibe archivos CSV diarios de un sistema de GPS de flota. Los suben a `Files/` (bronze), un notebook PySpark limpia coordenadas erróneas y las tipa correctamente, escribiéndolas como tabla Delta en `Tables/` (silver). Un analista de negocio, sin tocar Spark, consulta esa tabla desde el SQL analytics endpoint para construir vistas de kilometraje por vehículo (gold conceptual), y el informe de Power BI se conecta en Direct Lake: cuando llega el CSV del día siguiente y se reprocesa, el informe se actualiza solo, sin refresco programado.

### Knowledge check — respuestas (slide 33)
1. **Qué es un lakehouse de Fabric:** un almacén analítico que combina las capacidades de un data lake y un warehouse.
2. **Diferencia entre el explorador del lakehouse y el SQL endpoint:** el explorador gestiona los datos; el SQL endpoint da acceso T-SQL de solo lectura.
3. **Incluir datos de ADLS Gen2 sin copiarlos:** crear un shortcut que referencie los datos externos in situ.

### 💡 Detectado al revisar el deck
- El bloque "SQL vs. Spark" (slide 29) y el de "Direct Lake" (slide 30) son, según el propio guion, el contenido que más preguntas de examen genera — dedícales tiempo real en vez de pasar rápido, aunque el reloj apriete.
- La distinción "SQL endpoint = solo lectura" aparece tres veces en el módulo (slides 25, 29 y en el knowledge check) — es intencional, no una redundancia del deck: es el error de comprensión más frecuente entre alumnos que vienen de un mundo puramente relacional. Vale la pena remarcarla cada vez, no darla por sabida después de la primera.

---

## MÓDULO 4 — Primeros pasos con data warehouses
**Slides 35–46 · 70–80 min (incluye 30 min de ejercicio) · Learn:** get-started-data-warehouse

### Lo que se enseña vs. lo que NO se enseña
- ✅ Propósito del warehouse (OLTP vs. OLAP) · modelado dimensional (hechos, dimensiones, estrella, copo de nieve) · capacidades del warehouse de Fabric (T-SQL completo, OneLake, warehouse vs. SQL endpoint, consultas cross-DB) · capas de seguridad · consulta y transformación (editor SQL, editor visual, vistas, procedimientos) · modelado para informes (relaciones, medidas, Direct Lake)
- ❌ No entrar en: patrones SCD detallados, DAX avanzado, implementación de RLS/CLS, DMVs en profundidad, uso avanzado de Copilot

### Recorrido slide a slide

| # | Título | Punto clave para el aula |
|---|---|---|
| 36 | Learning objectives | Describir conceptos de warehouse y modelado dimensional; crear tablas y cargar datos; consultar/transformar con T-SQL y editor visual; modelar para informes. |
| 37 | ¿Qué es un data warehouse? | Consolida datos de varios sistemas operacionales en un único almacén analítico. `OLTP = muchas escrituras pequeñas` frente a `OLAP (warehouse) = lecturas analíticas complejas`. |
| 38 | Modelado dimensional | Tabla de hechos (medidas numéricas + claves foráneas, muchas filas) rodeada de tablas de dimensión (atributos descriptivos, pocas filas). Esquema en estrella = fact + dims a un salto. |
| 39 | Capacidades del warehouse de Fabric | T-SQL completo (DDL/DML/MERGE), totalmente gestionado, Copilot, consultas cross-DB con nombre en 3 partes, clones de tabla (zero-copy). **Warehouse = lectura/escritura · SQL analytics endpoint = solo lectura.** |
| 40 | Seguridad y monitorización del warehouse | 3 capas, de más ancha a más fina: roles de workspace → permisos de elemento → seguridad SQL granular (RLS, CLS, enmascarado). Monitorización: Query Insights (30 días) y DMVs (tiempo real). |
| 41 | Consultar y transformar datos | Editor SQL (T-SQL, IntelliSense, Copilot) vs. editor visual (arrastrar tablas, sin código). Ambos generan vistas/procedimientos reutilizables. |
| 42 | Modelar datos para informes y Direct Lake | Definir relaciones fact↔dim (muchos a uno) · crear medidas DAX reutilizables · limpiar metadatos (ocultar staging, renombrar, describir) · el modelo semántico usa Direct Lake por defecto. |
| 43 | Demo: crear warehouse, consultar y modelar | Crear warehouse → `CREATE TABLE` + `INSERT` → cargar esquema completo (`COPY INTO`) → `SELECT` con join fact/dim (IntelliSense) → crear vista → vista de modelo: crear relación, ocultar clave surrogate → opcional: editor visual. |
| 44 | Ejercicio: Analyze data in a warehouse | 30 min. Lab: `06-data-warehouse.html` |
| 45 | Knowledge check | 3 preguntas — ver respuestas abajo |
| 46 | Resumen de sección | Esquemas en estrella · T-SQL completo con auto-escalado · editor SQL/visual · relaciones y medidas, Direct Lake · capas de seguridad |

### Conceptos clave y definiciones (Módulo 4)

- **OLTP vs. OLAP:**
  | | OLTP | OLAP (warehouse) |
  |---|---|---|
  | Objetivo | Operar el negocio | Analizar el negocio |
  | Patrón | Muchas escrituras pequeñas | Lecturas y agregaciones grandes |
  | Modelo | Normalizado (3NF) | Desnormalizado, en estrella |
  | Ejemplo | Sistema de pedidos | Cuadro de mando de ventas |
- **Modelado dimensional (el corazón del módulo):**
  - **Tabla de hechos (fact):** contiene las **medidas numéricas** del proceso de negocio (importe, cantidad, coste) y las **claves foráneas** a las dimensiones. Muchas filas, pocas columnas. El **grano** (nivel de detalle de una fila) es la primera decisión de diseño.
  - **Tabla de dimensión (dim):** contiene los **atributos descriptivos** por los que se filtra y agrupa — cliente, producto, fecha, geografía. Pocas filas, muchas columnas.
  - **Esquema en estrella:** una fact rodeada de dimensiones, cada una a un solo salto. Preferido siempre que se pueda en Fabric/Power BI.
  - **Esquema en copo de nieve:** dimensiones normalizadas en varias tablas → más joins, peor rendimiento. Para analítica, no es "mejor por estar normalizado".
  - **Claves surrogate:** claves enteras propias del warehouse, no las del sistema origen.
  - **Slowly Changing Dimensions (SCD):** Tipo 1 sobrescribe el valor; Tipo 2 añade una fila nueva y conserva la historia con fechas de vigencia.
  - **Tabla de fechas:** siempre explícita, completa y marcada como tabla de fechas — base de toda la inteligencia de tiempo.
  - **Tabla de staging:** zona intermedia de carga, no se expone al negocio.
- **Capacidades del warehouse:** T-SQL completo (DDL, DML, `MERGE`, procedimientos almacenados) — **es LA diferencia con el SQL endpoint del lakehouse.** Totalmente gestionado, auto-escalado. Consultas entre bases con nombre en tres partes (`base.esquema.tabla`): un warehouse puede unir sus tablas con las de un lakehouse sin mover datos. Almacena en Delta Parquet sobre OneLake, igual que el lakehouse — misma base física, distinto motor de escritura.
- **Seguridad en dos niveles que la sala suele confundir:**
  - **Roles de workspace** (Admin, Member, Contributor, Viewer) → conceden acceso a **todos** los elementos del workspace.
  - **Permisos de elemento** → conceden acceso a **un** elemento concreto.
  - Dentro del warehouse, además: seguridad a nivel de objeto (`GRANT`), RLS con predicados, enmascarado dinámico de datos.
- **¿Lakehouse o warehouse?** (la pregunta que más cae):
  | Usa lakehouse si... | Usa warehouse si... |
  |---|---|
  | El equipo trabaja con Spark/Python | El equipo trabaja con T-SQL |
  | Hay datos no/semi-estructurados | Todo es relacional |
  | Necesitas notebooks y ML | Necesitas escrituras transaccionales en T-SQL |
  | Sigues el patrón medallón con ficheros raw | Haces modelado dimensional clásico |

  Ambos escriben en OneLake en Delta Parquet y se pueden consultar mutuamente — **la elección es de herramienta y perfil de equipo, no de capacidad técnica.**

### Ejemplo de aplicación
Una cadena de tiendas centraliza sus ventas: `FactSales` (grano = una línea de ticket) con claves foráneas a `DimProduct`, `DimStore`, `DimCustomer` y `DimDate`. El equipo de BI usa T-SQL con `MERGE` para cargar incrementalmente cada noche, crea una vista `vw_SalesDaily` que encapsula los joins habituales, y el modelo semántico oculta las claves surrogate y renombra `Qty` a "Unidades vendidas" para que Copilot y los informes de Power BI hablen el lenguaje del negocio. Un analista externo con permiso de elemento (no de workspace) solo ve ese warehouse, no el resto de activos del equipo.

### Knowledge check — respuestas (slide 45)
1. **Tabla para atributos de proveedores en una aseguradora:** tabla de dimensión.
2. **Qué da un warehouse que el SQL endpoint no da:** escribir datos con `INSERT`, `UPDATE`, `DELETE` y `MERGE`.
3. **Propósito de los permisos de elemento:** dar acceso a warehouses concretos para consumo posterior, sin abrir todo el workspace.

### 💡 Detectado al revisar el deck
- Es el módulo más denso conceptualmente (modelado dimensional + capacidades + seguridad + consulta + modelado para informes en una sola sesión) y el que más tiempo estimado tiene (70–80 min). Si vas de tiempo justo, es el mejor candidato para **no alargar** la parte de seguridad (slide 40) más allá de las tres capas — el propio deck ya marca RLS/CLS como "Not Taught" en detalle.
- La pregunta "¿Copo de nieve es mejor porque está normalizado?" (error de comprensión listado en las notas) suele generar debate con alumnos que vienen de modelado transaccional — resérvale 1–2 minutos extra si notas dudas, porque si no queda claro aquí, arrastra confusión al resto del curso sobre modelado.

---

## MÓDULO 5 — Primeros pasos con Real-Time Intelligence
**Slides 47–58 · 60–65 min (incluye 30 min de ejercicio) · Learn:** get-started-kusto-fabric

### Lo que se enseña vs. lo que NO se enseña
- ✅ Eventstreams (qué son, cómo fluye el dato en tiempo real) · Eventhouses y bases KQL (qué almacenan y por qué existen) · fundamentos de KQL (sintaxis pipe, patrones select/filter/aggregate)
- ❌ No entrar en: sintaxis KQL completa, políticas de actualización/vistas materializadas/funciones almacenadas, configuración de conectores del Real-Time hub, detalle de reglas de Activator, configuración avanzada de Real-Time Dashboards, Power BI + KQL, Copilot para RTI, Digital Twin Builder

### Recorrido slide a slide

| # | Título | Punto clave para el aula |
|---|---|---|
| 48 | Learning objectives | Describir analítica en tiempo real, eventos y streams; explicar cómo encajan los componentes; escribir KQL para seleccionar/filtrar/agregar; explicar cómo dashboards y Activator cierran el ciclo. |
| 49 | ¿Qué es Real-Time Intelligence? | Procesar eventos en segundos/minutos desde que ocurren, no en el lote nocturno. Evento = hecho con marca de tiempo; stream = flujo continuo ordenado por tiempo. También llamado "near real-time" — siempre hay algo de latencia de procesamiento. |
| 50 | Eventstreams y eventhouses | Eventstream captura y enruta (como fontanería: origen → transformación opcional → destino). Eventhouse almacena — contiene una o más bases KQL optimizadas para series temporales, ingesta de millones de eventos/seg. |
| 51 | Almacenar datos en un eventhouse | Jerarquía: `Eventhouse → base de datos KQL → tablas`. También contiene: querysets KQL, vistas materializadas, funciones almacenadas, shortcuts. |
| 52 | Consultar con KQL | Sintaxis pipe: tabla → operador → operador. Operadores núcleo: `take` (muestra, como TOP), `where` (filtra, como WHERE), `summarize` (agrega, como GROUP BY), `project` (selecciona, como SELECT). |
| 53 | Real-Time Dashboards y Activator | Dashboards: mosaicos conectados a consultas KQL, auto-refresco, sin botón de refresco. Activator: vigila condiciones (umbrales, patrones, ausencia) y dispara acciones (email, Teams, Power Automate). Cierra el ciclo: `Eventstream ingiere → Eventhouse almacena → KQL consulta → Dashboard visualiza → Activator actúa`. |
| 54 | Demo: Eventhouse, KQL y Real-Time dashboards | Abrir eventhouse → abrir queryset, `stock \| take 10` → añadir `where` → añadir `summarize` → mostrar mosaico de dashboard conectado a la consulta → opcional: menú "Get data". |
| 55 | Ejercicio: Get started with Real-Time Intelligence | 30 min. Lab: `07-real-time-Intelligence.html` |
| 56 | Knowledge check | 3 preguntas — ver respuestas abajo |
| 57 | Resumen de sección + cierre del Día 1 | Real-Time = eventos en segundos · Eventstreams capturan y enrutan · Eventhouses almacenan en bases KQL · patrón `table \| where \| summarize \| project` · Dashboards visualizan, Activator automatiza |
| 58 | Recursos | Enlaces a los 5 módulos de Microsoft Learn del día |

### Conceptos clave y definiciones (Módulo 5)

- **Vocabulario base:** *evento* = un hecho con marca de tiempo (lectura de sensor, clic, transacción). *Stream* = flujo continuo y no acotado de eventos. *Serie temporal* = eventos ordenados por tiempo, donde el tiempo es la dimensión principal de consulta.
- **Casos de uso que resuenan en el aula:** detección de fraude, telemetría de flotas, monitorización de plantas industriales, analítica de clickstream, alertas de inventario.
- **Componentes:**
  | Componente | Función |
  |---|---|
  | Real-Time Hub | Catálogo central de streams disponibles en el tenant |
  | Eventstream | Captura eventos de los orígenes y los enruta a destinos, sin código, con transformaciones en ruta |
  | Eventhouse | Contenedor de almacenamiento para series temporales |
  | Base de datos KQL | Vive dentro del eventhouse, contiene las tablas |
  | KQL queryset | Consultas guardadas y compartibles |
  | Real-Time Dashboard | Paneles con mosaicos que se auto-refrescan |
  | Activator | Vigila condiciones y dispara acciones |
  - Orígenes típicos de un eventstream: Azure Event Hubs, IoT Hub, Kafka, CDC de bases de datos, eventos de workspace de Fabric.
  - Destinos: eventhouse, lakehouse, otro eventstream, Activator, endpoint personalizado.
- **KQL (Kusto Query Language):** lenguaje de **solo lectura**, optimizado para exploración de series temporales. Se parte de una tabla, se encadenan operadores con el pipe (`|`), y el resultado de cada operador alimenta al siguiente.
  ```kql
  stock
  | where ["time"] > ago(5m)
  | summarize avgPrice = avg(toDouble(bidPrice)) by symbol
  | order by avgPrice desc
  ```
  **Operadores mínimos, con su equivalente SQL:**
  | KQL | Equivalente SQL | Qué hace |
  |---|---|---|
  | `take` / `limit` | `TOP` | Muestra N filas |
  | `where` | `WHERE` | Filtra |
  | `project` | `SELECT` | Selecciona columnas |
  | `extend` | — | Columna calculada |
  | `summarize` | `GROUP BY` | Agrega |
  | `order by` / `sort by` | `ORDER BY` | Ordena |
  | `ago()`, `bin()` | funciones de fecha | Ventanas e intervalos de tiempo |
  | `render` | — | Dibuja el resultado |
  - ⚠️ KQL es sensible a mayúsculas y el orden de los operadores importa (filtra antes de agregar).
  - Las bases KQL aceptan un subconjunto de T-SQL, pero KQL rinde mejor.
- **Dashboards y Activator:** el Real-Time Dashboard es vigilancia operativa (no informes ejecutivos — eso es Power BI). El Activator define condiciones sobre el stream (p. ej. *"temperatura > 80 ºC durante 3 lecturas consecutivas"*) y dispara una acción: correo, Teams, pipeline de Fabric o Power Automate.

### Ejemplo de aplicación
Una planta industrial tiene sensores de temperatura enviando lecturas cada segundo vía IoT Hub. Un **eventstream** las captura y las enruta a un **eventhouse**. Un analista escribe en KQL `sensores | where temp > 80 | summarize avg(temp) by bin(timestamp, 1m), maquina` para ver la media por minuto y por máquina, y construye un **Real-Time Dashboard** con un mosaico que muestra esa consulta en vivo. En paralelo, un **Activator** vigila el mismo stream: si la temperatura supera 80 °C durante 3 lecturas seguidas, envía un mensaje a Teams al equipo de mantenimiento — sin que nadie tenga que estar mirando el panel a las 3 de la madrugada.

### Knowledge check — respuestas (slide 56)
1. **Componente que ingiere y enruta streaming hacia Fabric:** Eventstream (el eventhouse almacena; los querysets KQL son para consultar).
2. **Qué hace el operador `where` de KQL:** filtra filas según una condición (equivalente a `WHERE`).
3. **Qué hace el operador `summarize`:** agrega valores en grupos (equivalente a `GROUP BY`).

### 💡 Detectado al revisar el deck
- Es el módulo con más "no enseñado" explícito de los cinco (8 puntos fuera de alcance) — es intencionadamente una introducción, el resto de RTI se retoma más adelante en el curso. **Si vas corto de tiempo al llegar aquí, este es el módulo donde más margen tienes para recortar sin comprometer el examen del día**, apoyándote en esa misma lista de "Not Taught".
- Vale la pena remarcar explícitamente el error de comprensión que trae el guion: *"un dashboard en tiempo real sustituye a Power BI"* — no, es vigilancia operativa frente a análisis de negocio. Es una distinción que conecta bien con lo visto en el Módulo 3 sobre Direct Lake, así que puedes usarla para enlazar ambos módulos.

---

## Cierre del Día 1 — los diez conceptos que debe llevarse el grupo

(Tal y como lo resume el propio guion del formador al final del Módulo 5; úsalo como resumen final de la jornada antes de despedir a la sala)

1. **Fabric** — SaaS, facturación por capacidad.
2. **OneLake** — un lago por tenant, una sola copia, Delta Parquet abierto.
3. **Shortcut** — referencia sin copia, permisos en el origen.
4. **Lakehouse** — Spark escribe, el endpoint SQL solo lee.
5. **Medallón** — Bronze crudo / Silver validado / Gold servido.
6. **Warehouse** — T-SQL completo sobre OneLake.
7. **Esquema en estrella** — hechos con medidas y claves, dimensiones con atributos.
8. **Direct Lake** — rendimiento de Import sin copia ni refresco, con fallback a DirectQuery.
9. **Real-Time Intelligence** — eventstream captura, eventhouse almacena, KQL consulta, Activator actúa.
10. **Gobierno** — endorsement y etiquetas de Purview viajan con el dato; roles de workspace frente a permisos de elemento.

---

## 2. Qué se podría añadir o mejorar

**Sobre el contenido:**
- El deck es inusualmente completo para un curso oficial — ya trae, incrustada en las notas, una guía del formador en español con conceptos clave, errores frecuentes y preguntas para la sala en los 5 módulos. Aprovéchala tal cual: es prácticamente un guion de clase ya escrito, no hace falta reinventarlo.
- **No echo en falta contenido técnico** — al contrario (ver §0). Si quieres añadir algo, mejor que sea *pegamento*, no más teoría:
  - Un **cuadro comparativo único** (lakehouse vs. warehouse vs. eventhouse, una sola tabla) que reutilices como diapositiva puente antes del Módulo 4 y otra vez al cerrar el Módulo 5 — el material ya lo compara dos a dos (lakehouse↔warehouse en la slide 35, eventhouse↔warehouse en la 51) pero nunca junta los tres a la vez, y es justo la pregunta que el propio guion marca como "la que más cae" el segundo día.
  - Una **demo end-to-end de 3 minutos** al final del día (fuera de las 5 horas oficiales, o como colofón si sobra tiempo) que muestre el mismo dato pasando por shortcut → lakehouse → warehouse → informe Direct Lake, para que el hilo conductor "una sola copia del dato" quede visualmente cerrado, no solo enunciado.
- Las slides 8 y 17 (ver notas arriba) son las dos donde el texto extraído es más difícil de seguir como guion hablado porque son diagramas complejos con etiquetas repetidas — no cambiaría el contenido, pero sí prepararía una frase de apoyo corta para cada una en vez de intentar narrar el diagrama completo.

**Sobre el ritmo (repitiendo lo esencial de §0):**
- El problema real de esta sesión no es de contenido sino de reloj: 305–340 minutos de material estimado por el propio curso frente a 300 minutos disponibles, sin contar descansos. Antes de dar la primera clase, decide ya qué vas a recortar si vas con retraso al llegar al Módulo 4, y no lo decidas sobre la marcha.

---

## Anexo: enlaces a ejercicios y módulos de Microsoft Learn

| Módulo | Ejercicio (lab) | Módulo de Microsoft Learn |
|---|---|---|
| 1 | — (sin ejercicio, solo demo) | learn.microsoft.com/training/modules/introduction-end-analytics-use-microsoft-fabric/ |
| 2 | Labs/25-discover-onelake.html | learn.microsoft.com/training/modules/discover-data-onelake/ |
| 3 | Labs/01-lakehouse.html | learn.microsoft.com/training/modules/get-started-lakehouses/ |
| 4 | Labs/06-data-warehouse.html | learn.microsoft.com/training/modules/get-started-data-warehouse/ |
| 5 | Labs/07-real-time-Intelligence.html | learn.microsoft.com/training/modules/get-started-kusto-fabric/ |

(Prefijo común de los labs: `https://microsoftlearning.github.io/mslearn-fabric/Instructions/`)
