# Customer: MongoDB

Customer uses MongoDB 7 and the `customer` database. The application manages the collections; the original project has no schema creation script or customer seed.

| Collection | Purpose |
| --- | --- |
| `clientes` | Documents with numeric `_id`, `nombre`, `telefono`, `email`, `direccion`, and `activo`. Deletion is logical. |
| `counters` | A document with `_id: "clientes"` and `seq`, used to assign numeric identifiers atomically. |
| `migration_status` | A document with `_id: "postgres_customer_v1"` recording completion of the PostgreSQL migration. |

MongoDB creates a unique `_id` index for each collection. Customer's code defines no additional MongoDB indexes. No sample data is included, and the migration marker is not inserted automatically.

## Standalone service

Set `MONGO_USER` and `MONGO_PASSWORD` using environment variables or an untracked local `.env` file. Then run `docker compose up -d`; the service exposes MongoDB only on `127.0.0.1:27018` by default. Use `MONGO_PORT` to choose another port. The initial user's authentication database is `admin`; Customer must set `MONGO_DATABASE=customer` and use the same credentials.

The `customer-mongodata` volume persists data. Initialization credentials apply only when a new volume is created.

## Migrating existing customers

The PostgreSQL migration is implemented in Customer API (`CustomerMigration`) and must be run separately using its deployment procedure. It reads existing customers, preserves their identifiers, and detects conflicts. The `migration_status` marker must only be created when that migration finishes; without it, Customer rejects normal startup by default. This repository does not run migrations or modify the existing database.
