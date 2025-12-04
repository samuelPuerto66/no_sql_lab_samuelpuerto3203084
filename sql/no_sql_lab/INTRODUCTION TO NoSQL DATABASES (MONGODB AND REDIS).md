# 1. PARADIGMAS DE BASES DE DATOS NoSQL

## 1.1 Contexto general de NoSQL

Las bases de datos NoSQL agrupan una familia de motores que se alejan del modelo relacional clásico (tablas, filas y columnas rígidas).  
La documentación de MongoDB identifica **cuatro tipos principales de bases de datos NoSQL**:

- **Documento (document databases)**
- **Clave–valor (key–value databases)**
- **Columna ancha o familia de columnas (wide-column stores)**
- **Grafo (graph databases)**

Cada paradigma está optimizado para un tipo de datos y patrón de acceso diferente, priorizando flexibilidad de esquema, escalabilidad horizontal y rendimiento en escenarios específicos.

---

## 1.2 Bases de datos orientadas a documentos (Document Databases)

### 1.2.1 Definición y concepto básico

Una base de datos orientada a documentos almacena los datos en **documentos** (generalmente JSON o BSON) que representan entidades completas: usuario, pedido, producto, etc.  
Cada documento se asocia a una **clave única** y puede contener estructuras complejas: arreglos, documentos anidados, valores simples, etc.

MongoDB es el ejemplo más representativo de este paradigma y almacena documentos en formato **BSON** (Binary JSON), manteniendo la estructura jerárquica del JSON.

### 1.2.2 Características clave

- **Modelo basado en documentos**: cada registro es un documento autocontenido (por ejemplo, un pedido completo).
- **Esquema flexible (schema-less o schema-flexible)**: documentos de una misma colección no tienen que compartir exactamente la misma estructura de campos.
- **Consultas ricas**: lenguajes como el **MongoDB Query Language (MQL)** permiten filtrar, proyectar, agrupar y actualizar documentos de forma expresiva.
- **Optimizado para objetos de dominio**: el documento se parece mucho al objeto que el desarrollador maneja en el código (por ejemplo, un objeto JSON en una API REST).

### 1.2.3 Ejemplo básico de documento (MongoDB)

```json
{
  "_id": "order_1001",
  "customerId": "cust_001",
  "status": "PAID",
  "items": [
    { "productId": "prod_01", "name": "Keyboard", "qty": 1, "price": 25.99 },
    { "productId": "prod_02", "name": "Mouse", "qty": 2, "price": 10.5 }
  ],
  "createdAt": "2025-11-26T10:30:00Z"
}
```

### 1.2.4 Casos de uso típicos

- APIs web y microservicios que ya manejan JSON.
- Sistemas donde el modelo de datos **cambia con frecuencia** (agregar/retirar campos sin migraciones costosas).
- Aplicaciones de contenido (blogs, catálogos de productos, perfiles de usuario).

---

## 1.3 Bases de datos clave–valor (Key–Value Databases)

### 1.3.1 Definición y concepto básico

Una base de datos clave–valor almacena datos como un gran diccionario distribuido:

- **Clave (key)**: identificador único.
- **Valor (value)**: dato asociado, que puede ser un string, un objeto serializado, un binario, etc.

Según la documentación de MongoDB, una base de datos clave–valor es un tipo de NoSQL donde los datos se guardan como **pares clave–valor**; la clave identifica de forma única al valor almacenado.

**Redis** es uno de los motores más populares de este paradigma. En la documentación oficial, Redis se describe como un servidor de estructuras de datos en memoria con tipos básicos como **strings**, y avanzados como **listas, sets, hashes y sorted sets**.

### 1.3.2 Características clave

- **Acceso por clave extremadamente rápido** (lookup O(1) típico).
- Modelo muy simple: no hay joins ni relaciones complejas.
- Ideal para almacenamiento **en caché**, sesiones de usuario, contadores, bandejas de cola, etc.
- En motores como Redis, los valores pueden ser estructuras más avanzadas (listas, hashes, sets), pero siguen siendo accesibles principalmente por clave.

  1.3.3 Ejemplo básico en Redis (clave–valor simple)

```bash
# Set a value
SET session:user_001 "{ \"userId\": \"user_001\", \"role\": \"student\" }"

# Get a value
GET session:user_001
```

### 1.3.4 Casos de uso típicos

- **Caché** para resultados de consultas costosas (por ejemplo, guardar una consulta SQL ya procesada).
- **Gestión de sesiones** (tokens de autenticación, datos de sesión HTTP).
- **Contadores y métricas en tiempo real** (visitas, likes, eventos).
- Sistemas de **cola** y mensajería simple (listas en Redis).

---

## 1.4 Bases de datos de grafos (Graph Databases)

### 1.4.1 Definición y concepto básico

Una base de datos de grafos almacena los datos como:

- **Nodos (nodes)**: representan entidades (persona, producto, ciudad, etc.).
- **Relaciones (relationships)**: conectan nodos, con un tipo y dirección (POR_EJEMPLO: `FRIEND_OF`, `BOUGHT`, `LOCATED_IN`).
- **Propiedades (properties)**: pares clave–valor asociados a nodos y relaciones.

La documentación de Neo4j explica que una base de datos de grafos almacena datos como **nodos, relaciones y propiedades**, en lugar de tablas o documentos, permitiendo representar de forma natural redes complejas.

Este modelo se conoce como **property graph model**.

### 1.4.2 Características clave

- Optimizado para **consultas de relaciones profundas** (varios “saltos”) como “amigos de mis amigos que viven en…”.
- Las relaciones son **ciudadanas de primera clase**, no resultados de joins como en SQL.
- Permite evolucionar el grafo añadiendo nuevos tipos de nodos y relaciones sin alterar un esquema rígido.
- Motores como Neo4j proporcionan lenguajes de consulta especializados, por ejemplo **Cypher**.

### 1.4.3 Ejemplo básico de consulta en Neo4j (Cypher)

```cypher
// Create two nodes and a relationship
CREATE (a:User {id: "user_001", name: "Ana"})
CREATE (b:User {id: "user_002", name: "Carlos"})
CREATE (a)-[:FOLLOWS]->(b);

// Query followers
MATCH (u:User)-[:FOLLOWS]->(other:User)
WHERE other.id = "user_002"
RETURN u.name AS follower;
```

### 1.4.4 Casos de uso típicos

- **Redes sociales** (amigos, seguidores, recomendaciones).
- **Sistemas de recomendación** (productos relacionados, “también compraron”).
- **Análisis de fraude** (detección de patrones sospechosos a través de conexiones).
- **Gestión de redes** (topologías, rutas, grafos de infraestructura).

---

## 1.5 Bases de datos de columnas anchas (Wide-Column Stores)

### 1.5.1 Definición y concepto básico

Las bases de datos de columnas anchas (o column-family stores) organizan los datos en:

- **Keyspace** (similar a una base de datos).
- **Tablas** (column families).
- **Filas (rows)** identificadas por una clave de partición.
- **Columnas dinámicas**, que pueden variar por fila, agrupadas lógicamente por familias de columnas.

La documentación de Apache Cassandra describe el motor como una base de datos NoSQL distribuida con un **modelo de almacenamiento de columnas anchas particionado**, diseñada para grandes volúmenes de datos y consultas a gran escala.

MongoDB también menciona las **wide-column stores** como uno de los cuatro tipos principales de NoSQL.

### 1.5.2 Características clave

- **Alta escalabilidad horizontal** (diseñadas para clusters con muchos nodos, datos distribuidos por particiones).
- Modelo **desnormalizado y orientado a consultas**: se diseñan las tablas pensando en cómo se van a consultar los datos, no en normalización estricta.
- Permiten columnas que **no aparecen en todas las filas**, lo que da cierta flexibilidad de esquema.
- Adecuadas para **series de tiempo, logs, analítica** con escrituras intensivas y lecturas predictivas.

### 1.5.3 Ejemplo básico de tabla en Cassandra (CQL)

```sql
-- Keyspace
CREATE KEYSPACE sample_project
WITH replication = {
  'class': 'SimpleStrategy',
  'replication_factor': 3
};

-- Wide-column style table (time-series)
CREATE TABLE sample_project.sensor_readings (
  sensor_id  TEXT,
  day        DATE,
  timestamp  TIMESTAMP,
  value      DOUBLE,
  PRIMARY KEY ((sensor_id, day), timestamp)
) WITH CLUSTERING ORDER BY (timestamp DESC);
```

En este ejemplo:

- La clave primaria está compuesta para distribuir por `sensor_id + day` y ordenar lecturas por `timestamp`.
- Esto permite consultas eficientes como “todas las lecturas recientes de un sensor en un día específico”.

  1.5.4 Casos de uso típicos

- **Series de tiempo** (IoT, sensores, métricas de servidores).
- **Sistemas de logging** y analítica de eventos.
- **Aplicaciones que requieren alta escritura distribuida** con disponibilidad muy alta.

---

## 1.6 Comparación rápida entre paradigmas NoSQL

| Paradigma       | Unidad principal de datos    | Esquema       | Ejemplos de motores     | Casos de uso típicos                            |
| --------------- | ---------------------------- | ------------- | ----------------------- | ----------------------------------------------- |
| Documento       | Documento (JSON/BSON)        | Flexible      | MongoDB, Couchbase      | APIs, contenido, modelos que cambian rápido     |
| Clave–valor     | Par clave–valor              | Muy simple    | Redis, Riak             | Caché, sesiones, contadores, colas              |
| Grafo           | Nodos + relaciones + props   | Flexible      | Neo4j, JanusGraph       | Redes sociales, recomendaciones, fraude         |
| Columnas anchas | Filas con columnas dinámicas | Semi-flexible | Apache Cassandra, HBase | Series de tiempo, logs, grandes volúmenes datos |

La elección del paradigma depende de:

- Cómo se **relacionan** los datos (relaciones profundas → grafo, objetos semiestructurados → documento).
- Cómo se van a **consultar** los datos (claves directas y acceso muy rápido → clave–valor; patrones de lectura por rangos y tiempo → columnas anchas).
- Requisitos de **escala, latencia, disponibilidad** y flexibilidad de esquema.

---

# 2. Diferencias conceptuales entre SQL y NoSQL

## 2.1 Visión general

En términos generales:

- Las bases de datos **SQL** (relacionales) usan un modelo basado en **tablas, filas y columnas**, con **esquemas rígidos** y fuerte énfasis en consistencia y relaciones (joins).
- Las bases de datos **NoSQL** agrupan varios modelos (documento, clave–valor, grafo, columnas anchas), ofreciendo **esquemas flexibles** y un diseño pensado para **escalar horizontalmente** y manejar grandes volúmenes de datos y cargas de usuario.

MongoDB, por ejemplo, se describe oficialmente como una base de datos NoSQL distribuida, con **esquema flexible** y capacidad de **escalado horizontal** nativo.

---

## 2.2 Esquema: rígido vs flexible

### 2.2.1 Esquema en SQL (relacional)

En motores como **PostgreSQL** y **MySQL**, el desarrollador define:

- **Tablas** con columnas y tipos de datos fijos (`CREATE TABLE`).
- **Restricciones** (PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK…).
- **Relaciones** entre tablas mediante claves foráneas.

Cualquier cambio en la estructura (agregar/quitar columna, cambiar tipo de dato) se realiza mediante **sentencias DDL** (`ALTER TABLE`) y, en sistemas grandes, implica **migraciones controladas** y tiempos de mantenimiento.

Ejemplo simple de esquema en SQL:

```sql
CREATE TABLE customers (
    customer_id   SERIAL PRIMARY KEY,
    full_name     VARCHAR(100) NOT NULL,
    email         VARCHAR(150) UNIQUE NOT NULL,
    created_at    TIMESTAMP NOT NULL DEFAULT NOW()
);
```

Este esquema es **estricto**: cada fila debe respetar esa estructura y tipos de datos.

### 2.2.2 Esquema en NoSQL (flexible)

En bases de datos **documento**, como MongoDB, los datos se almacenan en **documentos BSON** con estructura flexible. La propia documentación oficial de MongoDB destaca:

- **Esquema flexible**, que permite que el modelo de datos **evolucione** a medida que cambian las necesidades de la aplicación.

Ejemplo de dos documentos en una misma colección `customers`:

```json
{
  "_id": "cust_001",
  "fullName": "Ana López",
  "email": "ana@example.com",
  "phone": "+57 300 123 4567"
}

{
  "_id": "cust_002",
  "fullName": "Carlos Pérez",
  "email": "carlos@example.com",
  "loyaltyPoints": 120
}
```

Observa que:

- Ambos documentos están en la **misma colección**.
- El primer documento tiene `phone`, el segundo no.
- El segundo tiene `loyaltyPoints`, el primero no.

La base de datos acepta ambas variantes sin necesidad de modificar un esquema global.  
Sin embargo, MongoDB también permite definir **reglas de validación de esquema** usando JSON Schema para tener control cuando es necesario.

**Recomendaciones:**

- En **SQL**, pensar primero el **modelo lógico y físico** (tablas, tipos, relaciones) y luego implementarlo en el motor.
- En **NoSQL (documento)**, pueden iterar más rápido en el diseño del documento, pero se debe ser disciplinados en cómo estructuran la información para evitar caos.

---

## 2.3 Replicación y alta disponibilidad

La **replicación** es el mecanismo por el cual se mantienen **copias de los datos en varios servidores** para mejorar disponibilidad, tolerancia a fallos y, en algunos casos, lectura concurrente.

### 2.3.1 Replicación en SQL

#### PostgreSQL

La documentación oficial de PostgreSQL describe varios mecanismos:

- **Replicación física / streaming replication**: el servidor primario envía continuamente registros de WAL a uno o varios servidores en espera (standby).
- **Replicación lógica**: permite replicar cambios de datos a nivel de tablas/filas, con control más granular.

En general, la arquitectura típica es:

- Un **servidor primario** que acepta escrituras.
- Uno o varios **servidores réplica** que reciben los cambios y pueden servir lecturas.

#### MySQL

En MySQL, la documentación oficial define la replicación como el mecanismo por el cual un servidor “source” replica sus cambios a uno o más servidores “replicas”:

- Por defecto es **asíncrona**: la réplica puede ir “atrás” respecto al servidor principal.
- Se basa en un **binary log** donde se registran los cambios, que luego se reproducen en las réplicas.
- Se puede usar para **alta disponibilidad** y también para **scale-out de lectura** (distribuir lecturas entre varias réplicas).

### 2.3.2 Replicación en NoSQL

Algunos motores NoSQL fueron diseñados desde el inicio como **distribuidos y replicados**:

- **MongoDB**:

  - Implementa **replica sets**, que son agrupaciones de nodos donde uno actúa como **primario** y los demás como **secundarios**.
  - Soporta alta disponibilidad, failover automático y distribución geográfica.
  - La documentación de MongoDB resalta la **alta disponibilidad** y la **distribución** como características centrales de la plataforma.

- **Apache Cassandra** (wide-column store):
  - Desde su diseño, Cassandra almacena **réplicas en múltiples nodos** para lograr confiabilidad y tolerancia a fallos; el **factor de replicación** define cuántas copias existen de cada dato.
  - La arquitectura distribuida y la replicación en varios nodos y datacenters son parte de su esencia.

En muchos motores NoSQL, la replicación no es un “módulo adicional”, sino que forma parte del **núcleo del diseño** (por eso se habla tanto de clúster, nodos, particiones y réplicas).

---

## 2.4 Escalabilidad horizontal

### 2.4.1 Escalabilidad vertical vs horizontal

- **Vertical (scale-up)**: mejorar la capacidad de un solo servidor (más CPU, RAM, disco rápido).
- **Horizontal (scale-out)**: añadir más servidores al sistema y distribuir los datos y la carga de trabajo.

Los motores **SQL tradicionales** históricamente se han desplegado con enfoque principal en **escalabilidad vertical**, aunque existen soluciones para lectura distribuida (réplicas) y particionamiento (sharding).  
Los motores **NoSQL** modernos se diseñaron priorizando la **escalabilidad horizontal** desde el inicio.

### 2.4.2 Escalabilidad en SQL

- PostgreSQL y MySQL se pueden **replicar** para distribuir lecturas, pero la escritura suele concentrarse en un solo nodo primario.
- Existen soluciones de **sharding** y clúster (por ejemplo, MySQL NDB Cluster, extensiones y herramientas de terceros, particionamiento en PostgreSQL), pero requieren planificación y suelen ser más complejas de administrar.

Esto no significa que SQL no escale, sino que el modelo clásico está más alineado con **escalado vertical + réplicas para lectura**.

### 2.4.3 Escalabilidad en NoSQL

- **MongoDB**:

  - Ofrece **sharding** como mecanismo nativo para distribuir datos en múltiples nodos (shards) y así incrementar capacidad de almacenamiento y de procesamiento.
  - La documentación oficial destaca la **capacidad de escalar horizontalmente con sharding** como una de las ventajas principales.

- **Apache Cassandra**:
  - Logra **escalabilidad horizontal lineal** al particionar los datos mediante funciones de hash y replicarlos en múltiples nodos.
  - La documentación enfatiza que es un sistema de almacenamiento altamente escalable donde se pueden añadir o quitar nodos según sea necesario, sin punto único de fallo.

En estos motores, **agregar nodos al clúster** es la forma natural de aumentar capacidad (más almacenamiento, más throughput), y la arquitectura se encarga de redistribuir datos y carga.

---

## 2.5 Resumen : SQL vs NoSQL

- **Esquema**

  - SQL: esquema estructurado, definido por DDL, cambios mediante migraciones.
  - NoSQL: esquema flexible; en documento, cada registro puede variar, aunque se pueden definir validaciones opcionales.

- **Replicación**

  - SQL: disponible, pero generalmente como **módulo/feature de alta disponibilidad** que se configura (primario–réplicas).
  - NoSQL: la arquitectura tiende a ser **distribuida por defecto**, con réplicas y nodos como parte central del diseño.

- **Escalabilidad horizontal**
  - SQL: posible, pero a menudo más compleja (sharding manual o soluciones específicas).
  - NoSQL: pensada para **escala horizontal** desde el diseño (sharding, particionamiento, nodos distribuidos).

---

# 3. Instalación y configuración básica de MongoDB Community Server y Redis Open Source

## 3.1 Consideraciones previas

Antes de instalar:

- Usa un sistema operativo de desarrollo típico:
  - **Windows 10/11** (para MongoDB Community Server).
  - **Linux (Ubuntu LTS)** para ejemplos de línea de comandos (MongoDB y Redis).
- Necesitarás acceso con permisos de administrador:
  - En Windows: ejecutar instaladores como _Administrator_.
  - En Linux: usar `sudo` para instalar paquetes y servicios.

---

## 3.2 Instalación de MongoDB Community Server

### 3.2.1 Descarga de MongoDB Community Server

1. Abre el navegador y ve al sitio oficial de MongoDB.
2. Busca la sección **“Download MongoDB Community Server”**.
3. Selecciona:
   - **Edition**: _Community Server_.
   - **Version**: la versión estable recomendada (por ejemplo, 8.0.x).
   - **OS**: Windows, macOS o Linux según tu equipo.
4. Descarga el instalador correspondiente:
   - En Windows suele ser un archivo `.msi`.
   - En Linux lo habitual es seguir los pasos del repositorio `mongodb-org` (lo veremos abajo).

---

### 3.2.2 Instalación en Windows (usando el instalador MSI)

Pasos generales (resumen):

1. Ejecuta el archivo `mongodb-windows-x86_64-<version>.msi`.
2. Acepta los términos de licencia.
3. Selecciona tipo de instalación:
   - Recomendado para el curso: **Complete**.
4. Activa la opción:
   - **“Install MongoDB as a Service”** (para que se ejecute como servicio de Windows).
   - Deja el puerto por defecto **27017**.
5. Opcional: instalar **MongoDB Compass** (cliente gráfico para probar conexiones).
6. Finaliza el asistente.

Después de la instalación:

- MongoDB se ejecutará como servicio de Windows (normalmente `MongoDB Server`).
- Puedes verificar el servicio desde:
  - **Services** → buscar **MongoDB**.
- De forma predeterminada, el servicio usa un archivo de configuración `mongod.cfg` que define:
  - El `dbPath` (ruta de datos).
  - El puerto (`27017`).
  - Opciones de red básicas.

#### Arranque manual (modo desarrollo simple con ZIP)

Si usas la versión ZIP en lugar del MSI:

1. Descomprime el contenido en una carpeta, ejemplo:
   - `C:\mongodb\bin`
2. Crea la carpeta de datos:

   ```powershell
   mkdir C:\data\db
   ```

3. Inicia el servidor MongoDB:

   ```powershell
   C:\mongodb\bin\mongod.exe --dbpath "C:\data\db"
   ```

Este método es útil para pruebas puntuales (la ventana debe permanecer abierta).

---

### 3.2.3 Instalación en Linux (ejemplo en Ubuntu LTS con `apt`)

Ejemplo orientado a **Ubuntu 22.04/24.04 LTS** con el paquete oficial `mongodb-org`:

1. Instala dependencias básicas:

   ```bash
   sudo apt-get update
   sudo apt-get install -y gnupg curl
   ```

2. Importa la clave pública de MongoDB (ejemplo MongoDB 8.0):

   ```bash
   curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg \
   --dearmor
   ```

3. Crea el archivo de repositorio:

   ```bash
   echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] \
   https://repo.mongodb.org/apt/ubuntu $(lsb_release -sc)/mongodb-org/8.0 multiverse" | \
   sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
   ```

4. Actualiza índices e instala MongoDB Community:

   ```bash
   sudo apt-get update
   sudo apt-get install -y mongodb-org
   ```

5. Habilita e inicia el servicio `mongod`:

   ```bash
   sudo systemctl enable mongod
   sudo systemctl start mongod
   sudo systemctl status mongod
   ```

Si el estado indica `active (running)`, el servidor está levantado.

---

### 3.2.4 Verificar la instalación de MongoDB

Con el servidor en ejecución (Windows o Linux):

1. Abre una terminal (CMD/PowerShell en Windows, shell en Linux).
2. Ejecuta el cliente de MongoDB (shell moderna):

   ```bash
   mongosh
   ```

3. Dentro de `mongosh`, prueba comandos básicos:

   ```javascript
   // Ver bases de datos
   show dbs;

   // Crear / cambiar a una base de datos de prueba
   use sample_project;

   // Insertar un documento de prueba
   db.students.insertOne({
     name: "Ana",
     program: "databases",
     trimester: 3
   });

   // Consultar documentos
   db.students.find();
   ```

Si ves el documento insertado, la instalación está correcta.

---

### 3.2.5 Configuración básica de MongoDB (modo desarrollo)

En un entorno de desarrollo local, la configuración mínima suele incluir:

- **Puerto**: por defecto `27017`.
- **Dirección de escucha** (`bindIp`):
  - Para desarrollo local seguro: `127.0.0.1` (solo conexiones desde la misma máquina).
- **Ruta de datos** (`storage.dbPath`):
  - Debe apuntar a una carpeta existente con permisos adecuados.

Ejemplo (fragmento típico de configuración en `mongod.conf`):

```yaml
net:
  port: 27017
  bindIp: 127.0.0.1

storage:
  dbPath: /var/lib/mongodb
  journal:
    enabled: true
```

> En producción se agregarían parámetros de seguridad (`security.authorization`) y de replicación/sharding, pero para el curso nos centraremos en un nodo de desarrollo.

---

## 3.3 Instalación de Redis Open Source

### 3.3.1 Opciones para instalar Redis

Redis Open Source se puede instalar de varias formas:

- **Paquetes oficiales** para Linux (APT, RPM, Snap).
- **Compilación desde código fuente**.
- **Contenedores Docker** (útil en Windows, macOS o entornos de laboratorio).
- Servicios gestionados en la nube (Redis Cloud), para escenarios más avanzados.

Para el curso, usaremos principalmente:

- Instalación en **Linux (Ubuntu)**.
- Uso de **Docker** como alternativa rápida en cualquier sistema.

---

### 3.3.2 Instalación en Linux (Ubuntu usando repositorio oficial de Redis)

Ejemplo para Ubuntu:

1. Instala herramientas necesarias:

   ```bash
   sudo apt-get update
   sudo apt-get install -y lsb-release curl gpg
   ```

2. Importa la clave GPG de Redis:

   ```bash
   curl -fsSL https://packages.redis.io/gpg | \
   sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg
   ```

3. Agrega el repositorio de Redis:

   ```bash
   echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] \
   https://packages.redis.io/deb $(lsb_release -cs) main" | \
   sudo tee /etc/apt/sources.list.d/redis.list
   ```

4. Actualiza e instala Redis:

   ```bash
   sudo apt-get update
   sudo apt-get install -y redis
   ```

5. Habilita e inicia el servicio:

   ```bash
   sudo systemctl enable redis
   sudo systemctl start redis
   sudo systemctl status redis
   ```

---

### 3.3.3 Ejecutar Redis con Docker (útil en Windows)

Si cuentas con **Docker Desktop** (Windows, macOS o Linux), puedes ejecutar Redis así:

```bash
docker run -d --name redis-dev -p 6379:6379 redis:latest
```

- `-d`: ejecuta en segundo plano.
- `--name redis-dev`: nombre del contenedor.
- `-p 6379:6379`: expone el puerto 6379 del contenedor al host.

Con esto, tendrás un servidor Redis escuchando en `localhost:6379`.

---

### 3.3.4 Verificar la instalación de Redis

Una vez que el servidor Redis está en ejecución (servicio en Linux o contenedor Docker):

1. Abre una terminal.
2. Ejecuta el cliente de línea de comandos:

   ```bash
   redis-cli
   ```

3. Prueba el comando `PING`:

   ```bash
   redis-cli ping
   ```

   Deberías obtener:

   ```text
   PONG
   ```

4. Prueba operaciones clave–valor básicas:

   ```bash
   # Set a key
   redis-cli SET course "course_NoSQL"

   # Get the key
   redis-cli GET course

   # Increment a counter
   redis-cli INCR page_visits

   # Check the value
   redis-cli GET page_visits
   ```

Si obtienes respuestas correctas (`"course_NoSQL"`, números que aumentan, etc.), la instalación funciona.

---

### 3.3.5 Configuración básica de Redis

Redis utiliza un archivo de configuración típico llamado `redis.conf`. Algunos parámetros clave para desarrollo:

- **`port`**: por defecto `6379`.
- **`bind`**: interfaz de red en la que escucha Redis (por ejemplo `127.0.0.1` para solo local).
- **`protected-mode`**: normalmente `yes` para evitar accesos remotos accidentales.
- **`requirepass`**: permite definir una contraseña para los clientes.

Fragmento típico:

    ```conf
    bind 127.0.0.1
    port 6379
    protected-mode yes
    # requirepass myStrongPassword
    ```

En modo laboratorio (solo en tu máquina y sin exponer a Internet), se puede trabajar con la configuración por defecto, pero es importante entender que en producción **siempre** se debe ajustar seguridad (firewall, contraseña, red interna, etc.).

---

# 4. Conceptos fundamentales de MongoDB y Redis

## 4.1 MongoDB: bases de datos, colecciones y documentos JSON/BSON

### 4.1.1 Bases de datos y colecciones

En MongoDB, la organización lógica es:

- **Base de datos (database)**: agrupa varias colecciones.
- **Colecciones (collections)**: agrupan documentos relacionados (similar a una “tabla” en SQL, pero sin esquema rígido).
- **Documentos (documents)**: registros individuales en formato BSON.

MongoDB almacena los registros como **documentos BSON**, que se agrupan en colecciones dentro de una base de datos. BSON es una representación binaria de JSON.

Ejemplo de estructura lógica:

- Base de datos: `school`
  - Colección: `students`
  - Colección: `courses`

### 4.1.2 Documentos JSON/BSON

Un documento en MongoDB se representa normalmente como JSON cuando lo vemos en la shell, aunque internamente se almacena como BSON.

```json
{
  "_id": "stu_001",
  "name": "Ana López",
  "program": "Software Development",
  "trimester": 3,
  "skills": ["databases", "git", "javascript"],
  "contact": {
    "email": "ana@example.com",
    "phone": "+57 300 123 4567"
  }
}
```

Características importantes:

- Cada documento tiene un identificador `_id` único.
- Se permiten **documentos anidados** y **arreglos**.
- Los documentos de una misma colección pueden tener campos diferentes (esquema flexible).

---

## 4.2 MongoDB: operaciones CRUD básicas

Las operaciones CRUD (Create, Read, Update, Delete) son la base del trabajo con datos en MongoDB.

### 4.2.1 Create – insertar documentos

Inserta uno o varios documentos en una colección.

```javascript
// Usando mongosh
use school;

// Insertar un documento
db.students.insertOne({
  name: "Ana López",
  program: "Software Development",
  trimester: 3
});

// Insertar múltiples documentos
db.students.insertMany([
  { name: "Carlos Pérez", program: "Software Development", trimester: 3 },
  { name: "Luisa Gómez",  program: "Network Management",   trimester: 2 }
]);
```

### 4.2.2 Read – consultar documentos

Consulta documentos usando filtros.

```javascript
// Todos los documentos
db.students.find();

// Filtrar por un campo
db.students.find({ program: "Software Development" });

// Proyección de campos (solo name y program)
db.students.find({ trimester: 3 }, { name: 1, program: 1, _id: 0 });
```

### 4.2.3 Update – actualizar documentos

Modifica campos de uno o varios documentos.

```javascript
// Actualizar un documento (primer match)
db.students.updateOne(
  { name: "Ana López" }, // filtro
  { $set: { trimester: 4 } } // operación de actualización
);

// Actualizar múltiples documentos
db.students.updateMany(
  { program: "Software Development" },
  { $set: { active: true } }
);
```

### 4.2.4 Delete – eliminar documentos

Elimina uno o varios documentos de una colección.

```javascript
// Eliminar un documento
db.students.deleteOne({ name: "Luisa Gómez" });

// Eliminar varios documentos
db.students.deleteMany({ active: false });
```

Estas operaciones CRUD son suficientes para cubrir la mayoría de ejercicios iniciales: inserción de datos de prueba, consultas con filtros sencillos y actualización/eliminación básica.

---

## 4.3 Redis: modelo clave–valor y TTL

### 4.3.1 Claves y valores en Redis

Redis se define como un “data structure server”, pero la idea central sigue siendo el **modelo clave–valor**:

- **Key (clave)**: nombre único del dato.
- **Value (valor)**: dato asociado (string, lista, hash, set, etc.).

Ejemplo simple:

```bash
# Asignar un valor
SET app:name "my_web_app"

# Leer un valor
GET app:name
```

### 4.3.2 TTL (Time To Live) y expiración de claves

Redis permite asociar un **tiempo de vida (TTL)** a una clave. Cuando el TTL llega a cero, la clave se elimina automáticamente.

```bash
# Definir una clave con TTL de 60 segundos
SET session:user_001 "logged_in"
EXPIRE session:user_001 60

# Consultar el TTL restante
TTL session:user_001
```

Uso típico:

- Sesiones de usuario.
- Caché temporal de consultas.
- Datos que no deben vivir indefinidamente en memoria.

---

## 4.4 Redis: hashes, listas, sets y sorted sets

Redis incluye varios tipos de datos nativos que amplían el modelo clave–valor:

### 4.4.1 Hashes

Un **hash** es una colección de pares campo–valor dentro de una misma clave. Se suele usar para representar objetos sencillos.

```bash
# Crear un hash para un usuario
HSET user:1001 name "Ana" email "ana@example.com" program "Software Development"

# Obtener un campo
HGET user:1001 email

# Obtener todos los campos
HGETALL user:1001
```

Uso típico: almacenar perfiles de usuario, configuraciones, contadores agrupados, etc.

---

### 4.4.2 Lists (listas)

Una **lista** es una secuencia ordenada de strings (similar a una cola o pila).

```bash
# Agregar elementos al final de la lista
RPUSH tasks "task_1" "task_2" "task_3"

# Obtener los elementos
LRANGE tasks 0 -1

# Sacar un elemento del principio (cola)
LPOP tasks
```

Usos frecuentes:

- Colas simples de trabajo.
- Historial de eventos.
- Listas de mensajes recientes.

---

### 4.4.3 Sets (conjuntos)

Un **set** es una colección de strings **únicos** y sin orden garantizado.

```bash
# Agregar miembros a un set
SADD active_users "user_001" "user_002" "user_003"

# Ver miembros
SMEMBERS active_users

# Verificar pertenencia
SISMEMBER active_users "user_002"
```

Usos frecuentes:

- Listas de usuarios conectados.
- Colecciones sin duplicados (etiquetas, permisos).
- Operaciones de teoría de conjuntos (intersección, unión, diferencia).

---

### 4.4.4 Sorted Sets (conjuntos ordenados)

Un **sorted set** es similar a un set (miembros únicos), pero cada miembro tiene asociado un **score numérico**. Los miembros se mantienen ordenados por ese score.

```bash
# Agregar jugadores con su puntaje
ZADD leaderboard 150 "ana" 200 "carlos" 120 "luisa"

# Obtener ranking del más bajo al más alto
ZRANGE leaderboard 0 -1 WITHSCORES

# Obtener top 2 (mayores puntajes)
ZREVRANGE leaderboard 0 1 WITHSCORES
```

Usos frecuentes:

- Rankings (puntuaciones de juegos, usuarios más activos).
- Listas ordenadas por fecha/hora usando timestamps como score.
- Sistemas de “top N” (ej. productos más vistos).

---

## 4.5 Resumen práctico para los ejercicios

- **MongoDB**:
  - Trabajarás con **bases de datos**, **colecciones** y **documentos JSON/BSON**.
  - Aplicarás operaciones CRUD usando `insertOne`, `find`, `updateOne`, `deleteOne`, etc.
- **Redis**:
  - Empezarás con el modelo **clave–valor** y el uso de **TTL** para expiración de claves.
  - Explorarás tipos de datos clave:
    - Hashes para objetos simples.
    - Lists para colas e historiales.
    - Sets para colecciones sin duplicados.
    - Sorted sets para rankings y datos ordenados por score.

Estas ideas serán la base para las prácticas de la semana, donde conectarás estas operaciones con un proyecto o aplicación (por ejemplo, manejo de sesiones, caché de datos y almacenamiento de entidades en MongoDB).

---

# 5. Casos de uso reales para cada motor (MongoDB y Redis)

## 5.1 MongoDB: casos de uso típicos

MongoDB es una base de datos orientada a documentos, flexible y preparada para escalar horizontalmente. Esto la hace adecuada para muchos escenarios de aplicaciones modernas.

### 5.1.1 Aplicaciones web y móviles (backend de APIs REST/GraphQL)

**Contexto:**  
Aplicaciones que exponen APIs REST o GraphQL y manejan datos en formato JSON: usuarios, productos, pedidos, publicaciones, comentarios, etc.

**Por qué MongoDB encaja bien:**

- El documento BSON se mapea de forma natural a JSON que se envía/recibe en la API.
- Evolución rápida del modelo (añadir campos sin migraciones complicadas).
- Índices secundarios, consultas flexibles y agregaciones para filtrar/ordenar datos.

**Ejemplo sencillo de escenario:**

- Backend de un sistema de reservas, un e-commerce o una app de mensajería:
  - Colecciones: `users`, `products`, `orders`, `messages`.
  - Cada colección almacena documentos con estructura similar a los objetos que devuelve la API.

---

### 5.1.2 “Single View” o vista unificada de cliente/producto

**Contexto:**  
Una organización tiene datos de un mismo cliente repartidos en varios sistemas: CRM, facturación, soporte, marketing, etc. Se requiere una **vista unificada** para atención al cliente, análisis o personalización.

**Por qué MongoDB encaja bien:**

- Los documentos pueden combinar datos de múltiples fuentes en un solo registro rico por cliente.
- Esquema flexible para ir añadiendo nuevas secciones (ej. “preferencias de marketing”, “historial de soporte”).
- Integración con pipelines ETL/ELT y herramientas de sincronización de datos.

**Ejemplo sencillo:**

- Colección `customers` en la que cada documento contiene:
  - Datos básicos del cliente.
  - Lista de pedidos recientes.
  - Tickets de soporte abiertos.
  - Preferencias de comunicación.

---

### 5.1.3 Gestión de contenido (CMS, catálogos, blogs)

**Contexto:**  
Sistemas donde los tipos de contenido cambian con frecuencia: blogs, noticias, páginas de marketing, catálogos de productos, documentación en línea.

**Por qué MongoDB encaja bien:**

- Cada tipo de contenido puede tener campos distintos (artículo, página estática, producto, tutorial).
- Es sencillo agregar nuevos tipos de contenido o propiedades sin alterar un esquema global.
- Integración con motores de búsqueda (por ejemplo, capacidades de búsqueda de texto y filtros avanzados).

**Ejemplo sencillo:**

- Colección `articles` donde:
  - Algunos documentos tienen campos de “autor invitado”.
  - Otros tienen campos de “serie” o “categoría especial”.
  - La aplicación puede ignorar/usar campos según corresponda.

---

### 5.1.4 IoT y manejo de eventos (sensores, logs, métricas)

**Contexto:**  
Dispositivos que envían datos de sensores (temperatura, ubicación, consumo de energía, etc.) o aplicaciones que generan eventos y logs a alta frecuencia.

**Por qué MongoDB encaja bien:**

- Cada documento puede representar una lectura de sensor o un evento.
- El modelo flexible permite añadir nuevos tipos de métricas sin alterar lo anterior.
- Se pueden usar colecciones específicas para series de tiempo, particionando por fechas o dispositivos.

**Ejemplo sencillo:**

- Colección `device_readings`:
  - Campos básicos: `deviceId`, `timestamp`, `value`.
  - Campos opcionales para distintos tipos de sensores: `temperature`, `humidity`, `batteryLevel`, etc.

---

### 5.1.5 Aplicaciones de pagos y transacciones modernas

**Contexto:**  
Plataformas de pago, billeteras digitales, marketplaces, que requieren manejar transacciones y estado de órdenes.

**Por qué MongoDB encaja bien:**

- Soporta transacciones ACID de múltiples documentos cuando se necesitan operaciones consistentes.
- Permite modelar órdenes y pagos como documentos completos, que incluyen historiales y estados.
- Escala horizontalmente para manejar picos de tráfico (ej. fechas especiales, campañas).

**Ejemplo sencillo:**

- Colección `payments`:
  - Documento con detalles del pago, estado, método, respuesta del gateway, etc.
  - Actualización de estado (`PENDING` → `APPROVED` / `REJECTED`) usando transacciones cuando se afecten varios documentos.

---

## 5.2 Redis: casos de uso típicos

Redis es un motor en memoria con múltiples estructuras de datos, muy usado para mejorar el rendimiento y manejar información en tiempo real.

### 5.2.1 Caché de datos (resultados de consultas, páginas, fragmentos)

**Contexto:**  
Aplicaciones que consultan frecuentemente una base de datos más lenta (SQL o NoSQL) o servicios externos (APIs), y necesitan respuestas rápidas.

**Por qué Redis encaja bien:**

- Respuestas de lectura en memoria con latencias de milisegundos o menos.
- Alivia la carga de la base de datos principal.
- TTL para invalidar automáticamente datos obsoletos.

**Ejemplo sencillo:**

- Al consultar la ficha de un producto:
  - La aplicación primero busca en Redis por una clave `product:<id>`.
  - Si existe, devuelve el JSON desde Redis.
  - Si no, consulta la base de datos principal, guarda el resultado en Redis con TTL (por ejemplo, 5 minutos) y lo devuelve al usuario.

---

### 5.2.2 Gestión de sesiones de usuario y autenticación

**Contexto:**  
Aplicaciones web con muchos usuarios concurrentes necesitan un lugar centralizado y muy rápido para almacenar sesiones (tokens, datos temporales de usuario).

**Por qué Redis encaja bien:**

- Modelo clave–valor con TTL ideal para sesiones que expiran.
- Acceso rápido desde múltiples servidores de aplicación.
- Permite invalidar sesiones (borrar claves) cuando sea necesario.

**Ejemplo sencillo:**

- Clave `session:<token>` con un objeto que representa la sesión:
  - `SET` con TTL (ej. 30 minutos).
  - Renovación de TTL con cada petición.
  - Eliminación de sesión al cerrar sesión explícitamente.

---

### 5.2.3 Leaderboards y rankings en tiempo real (sorted sets)

**Contexto:**  
Juegos en línea, plataformas de aprendizaje, aplicaciones de reputación o gamificación.

**Por qué Redis encaja bien:**

- Los **sorted sets (ZSET)** permiten mantener un ranking ordenado usando el score (puntos, experiencia, nivel).
- Actualizaciones rápidas al subir de posición.
- Fácil consulta de “top N” o rango de posiciones.

**Ejemplo sencillo:**

- Clave `leaderboard:game1` (sorted set):
  - Cada miembro es un `userId`.
  - El score es el puntaje acumulado.
  - Se usan comandos como `ZADD`, `ZRANGE`, `ZREVRANGE`.

---

### 5.2.4 Colas de trabajo y procesamiento asíncrono (listas)

**Contexto:**  
Procesos que se deben ejecutar en segundo plano: envío de correos, generación de reportes, procesamiento de imágenes, notificaciones, etc.

**Por qué Redis encaja bien:**

- Las **listas** permiten implementar colas simples (FIFO).
- Varios workers pueden consumir de la misma cola.
- Comandos como `LPUSH`/`BRPOP` permiten un patrón productor–consumidor.

**Ejemplo sencillo:**

- Cola `email_jobs`:
  - La aplicación web empuja tareas: `LPUSH email_jobs <json del correo>`.
  - Uno o varios workers ejecutan `BRPOP email_jobs 0` para tomar el siguiente trabajo y procesarlo.

---

### 5.2.5 Contadores, métricas y rate limiting

**Contexto:**

- Contar visitas de página, likes, descargas, eventos.
- Implementar **rate limiting** (limitar peticiones por usuario/IP en un intervalo de tiempo).

**Por qué Redis encaja bien:**

- Operaciones atómicas de incremento (`INCR`, `INCRBY`).
- TTL en claves para definir ventanas de tiempo.
- Gran rendimiento para altos volúmenes de actualizaciones concurrentes.

**Ejemplo sencillo de rate limiting:**

- Clave `rate:<userId>:<minute>`:
  - Cada petición hace `INCR` sobre esa clave.
  - La primera vez se le asigna un TTL de 60 segundos.
  - Si el valor supera un umbral (por ejemplo, 100), se bloquean más peticiones de ese usuario en ese minuto.

---

### 5.2.6 Chat, mensajería y pub/sub

**Contexto:**  
Aplicaciones de chat, notificaciones en tiempo real, canales de eventos.

**Por qué Redis encaja bien:**

- Redis ofrece un sistema **pub/sub** ligero:
  - Productores publican mensajes en un canal.
  - Suscriptores reciben los mensajes en tiempo real.
- Es útil para notificaciones, paneles de control en tiempo real y comunicación entre servicios.

**Ejemplo sencillo:**

- Canal `chat:room1`:
  - Clientes suscritos reciben los mensajes publicados por otros clientes en ese canal.

---

## 5.3 Relación con una arquitectura típica moderna

En muchos sistemas modernos se combinan ambos motores:

- **MongoDB** como base de datos principal para almacenar entidades de negocio: usuarios, pedidos, productos, contenido.
- **Redis** como capa de:
  - Caché para consultas más costosas.
  - Almacenamiento temporal de sesiones, tokens y resultados intermedios.
  - Colas para tareas asíncronas y métricas en tiempo real.

Este enfoque híbrido permite:

- Mantener un modelo de datos rico y flexible en MongoDB.
- Optimizar rendimiento y experiencia de usuario con Redis como apoyo en memoria.
