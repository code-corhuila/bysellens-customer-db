# Customer: MongoDB

Customer utiliza MongoDB 7 y la base de datos `customer`. La aplicación administra las colecciones; no existe un script de creación de esquema ni un seed de clientes en el proyecto original.

| Colección | Uso |
| --- | --- |
| `clientes` | Documentos con `_id` numérico, `nombre`, `telefono`, `email`, `direccion` y `activo`. La eliminación es lógica. |
| `counters` | Documento `_id: "clientes"` con `seq`, usado para asignar identificadores numéricos de forma atómica. |
| `migration_status` | Documento `_id: "postgres_customer_v1"` que registra la finalización de la migración desde PostgreSQL. |

MongoDB crea el índice único de `_id` por colección. El código de Customer no define índices adicionales para MongoDB. No se incluyen datos de ejemplo ni se inserta el marcador de migración automáticamente.

## Servicio independiente

Configure `MONGO_USER` y `MONGO_PASSWORD` mediante variables de entorno o un archivo `.env` local no versionado. Después ejecute `docker compose up -d`; el servicio publica MongoDB únicamente en `127.0.0.1:27018` por defecto. `MONGO_PORT` permite elegir otro puerto. La base de autenticación del usuario inicial es `admin`; Customer debe configurar `MONGO_DATABASE=customer` y las mismas credenciales.

El volumen `customer-mongodata` conserva los datos. Las credenciales de inicialización solo se aplican al crear un volumen nuevo.

## Migración de clientes existentes

La migración desde PostgreSQL se implementa en Customer API (`CustomerMigration`) y debe ejecutarse por separado bajo su procedimiento de despliegue. Lee los clientes existentes, conserva sus identificadores y detecta conflictos. El marcador de `migration_status` solo debe crearse al finalizar esa migración; sin él, Customer rechaza el arranque normal por defecto. Este repositorio no ejecuta migraciones ni modifica la base existente.
