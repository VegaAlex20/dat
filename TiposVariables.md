# Tipos de Datos en PostgreSQL para Inserciones SQL

Este documento resume los principales **tipos de datos** que puedes usar al insertar valores en una base de datos PostgreSQL, organizados por categoría.

---

## 📌 Numéricos

| Tipo         | Descripción                          | Alias           |
|--------------|--------------------------------------|-----------------|
| `smallint`   | Entero pequeño de 2 bytes            | `int2`          |
| `integer`    | Entero estándar de 4 bytes           | `int`, `int4`   |
| `bigint`     | Entero grande de 8 bytes             | `int8`          |
| `decimal(p,s)` | Número exacto de precisión fija    | -               |
| `numeric(p,s)` | Igual a `decimal`, sin redondeo    | -               |
| `real`       | Número de punto flotante de 4 bytes  | `float4`        |
| `double precision` | Punto flotante de 8 bytes     | `float8`        |
| `serial`     | Entero auto-incremental de 4 bytes   | -               |
| `bigserial`  | Entero auto-incremental de 8 bytes   | -               |

---

## 📌 Caracteres (Texto)

| Tipo         | Descripción                          |
|--------------|--------------------------------------|
| `char(n)`    | Cadena de longitud fija              |
| `varchar(n)` | Cadena de longitud variable          |
| `text`       | Cadena de longitud ilimitada         |

---

## 📌 Booleanos

| Tipo       | Descripción               |
|------------|---------------------------|
| `boolean`  | Verdadero (`TRUE`) o falso (`FALSE`) |

---

## 📌 Fechas y Tiempos

| Tipo                  | Descripción                              |
|-----------------------|------------------------------------------|
| `date`                | Fecha (AAAA-MM-DD)                       |
| `time`                | Hora (sin zona horaria)                  |
| `time with time zone`| Hora con zona horaria (`timetz`)         |
| `timestamp`           | Fecha y hora (sin zona horaria)          |
| `timestamp with time zone` | Fecha y hora con zona horaria (`timestamptz`) |

---

## 📌 Binarios

| Tipo        | Descripción                      |
|-------------|----------------------------------|
| `bytea`     | Datos binarios ("byte array")    |

---

## 📌 UUID

| Tipo    | Descripción                              |
|---------|------------------------------------------|
| `uuid`  | Identificador único universal            |

---

## 📌 Enumerados

| Tipo       | Descripción                                    |
|------------|------------------------------------------------|
| `enum`     | Tipo definido por el usuario con valores fijos |

> Ejemplo:
> ```sql
> CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy');
> ```

---

## 📌 JSON / JSONB

| Tipo     | Descripción                     |
|----------|---------------------------------|
| `json`   | Datos JSON (texto plano)        |
| `jsonb`  | JSON en formato binario optimizado |

---

## 📌 Arreglos

- Cualquier tipo puede ser representado como arreglo.
> Ejemplo: `integer[]`, `text[]`

---

## 📌 Geométricos y Espaciales

| Tipo         | Descripción                |
|--------------|----------------------------|
| `point`      | Punto (x, y)               |
| `line`       | Línea infinita             |
| `lseg`       | Segmento de línea          |
| `box`        | Rectángulo                 |
| `circle`     | Círculo                    |
| `polygon`    | Polígono cerrado           |

---

## 📌 Otros

| Tipo         | Descripción                          |
|--------------|--------------------------------------|
| `money`      | Valor monetario                      |
| `interval`   | Intervalo de tiempo                  |
| `tsvector`   | Texto indexable para búsqueda full-text |
| `cidr`, `inet` | Direcciones IP                     |
| `macaddr`    | Direcciones MAC                      |
| `xml`        | Datos XML                            |

---

## ✅ Ejemplo de Inserción

```sql
INSERT INTO empleados (nombre, edad, salario, contratado, fecha_ingreso)
VALUES ('Ana', 30, 2500.75, TRUE, '2025-05-08');
