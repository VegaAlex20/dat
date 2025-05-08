
# 📊 Modelo Estrella de Atención al Cliente en PostgreSQL

Este modelo define un esquema de base de datos en PostgreSQL para almacenar y analizar registros de atención al cliente provenientes de diferentes sucursales, operadores y horarios. 

---

## ⭐ ¿Qué es un Modelo Estrella?

Un **modelo estrella** es una forma de estructurar una base de datos para análisis eficiente. Se compone de:

- **Una tabla central de hechos** (`atencion`) con datos numéricos o medibles.
- **Varias tablas de dimensiones** que describen los contextos (como `sucursal`, `operador`, `tiempo_dim`).

Esta estructura permite generar reportes dinámicos, rápidos y claros.

---

## 🧱 Tablas Incluidas

### 1. `sucursal`
Contiene el nombre único de cada sucursal.  
Uso: filtrar reportes por sucursal.

```sql
CREATE TABLE sucursal (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE
);
```

---

### 2. `operador`
Contiene el nombre del operador que atiende a los clientes.  
Uso: agrupar reportes por agente.

```sql
CREATE TABLE operador (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE
);
```

---

### 3. `tiempo_dim`
Dimensión de tiempo enriquecida con día, mes, año, día de la semana y franja horaria.  
Uso: análisis por periodos de tiempo y horarios.

```sql
CREATE TABLE tiempo_dim (
    id SERIAL PRIMARY KEY,
    fecha DATE NOT NULL,
    dia INTEGER,
    mes INTEGER,
    anio INTEGER,
    dia_semana VARCHAR(15),
    franja_horaria VARCHAR(20)
);
```

---

### 4. `atencion` (tabla de hechos)
Registra cada atención con detalles de tiempo, operador, sucursal y métricas clave.  
Es el centro del modelo de análisis.

```sql
CREATE TABLE atencion (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(10),
    numero VARCHAR(10),
    mesa VARCHAR(10),
    estado VARCHAR(15),
    tiempo_atencion VARCHAR(10),
    segundos_atencion INTEGER,
    tiempo_espera VARCHAR(10),
    segundos_espera INTEGER,
    tiempo_total INTEGER,
    hora_ingreso TIME,
    hora_inicio TIME,
    hora_fin TIME,
    minuto_ingreso INTEGER,
    operador_id INTEGER REFERENCES operador(id),
    sucursal_id INTEGER REFERENCES sucursal(id),
    tiempo_id INTEGER REFERENCES tiempo_dim(id),
    kardex VARCHAR(50),
    celular VARCHAR(20)
);
```

---

## ✅ Ventajas del Modelo Estrella

- 💡 **Consultas más simples y rápidas** en herramientas como Power BI.
- 📅 **Análisis temporal flexible** (por día, semana, franja horaria).
- 🧍 **Comparaciones entre operadores y sucursales.**
- 📉 **Métricas como tiempo promedio de atención o espera** por cualquier dimensión.

---






# 🐼 Limpieza y Enriquecimiento de Datos con Pandas

Este script en Python utiliza la librería `pandas` para transformar un archivo Excel con registros de atención al cliente. Las transformaciones permiten preparar los datos para análisis en Power BI y para cargarlos en un modelo de base de datos en PostgreSQL.

---

## 📌 ¿Qué hace este código paso a paso?

### 1. 🗓️ Conversión de fechas

```python
df["FECHA"] = pd.to_datetime(df["FECHA"], dayfirst=True)
df["DIA"] = df["FECHA"].dt.day
df["MES"] = df["FECHA"].dt.month
df["ANIO"] = df["FECHA"].dt.year
df["DIA_SEMANA"] = df["FECHA"].dt.day_name()
```

- Convierte la columna `FECHA` a formato de fecha (`datetime`).
- Extrae día, mes, año y nombre del día (ej: Monday, Tuesday).
- Esto ayuda a hacer filtros por calendario en Power BI.

---

### 2. 🕐 Conversión de hora de ingreso

```python
df["HORA_INGRESO"] = pd.to_datetime(df["HORA_INGRESO"], format="%H:%M:%S", errors="coerce")
df["MINUTO_INGRESO"] = df["HORA_INGRESO"].dt.minute
df["FRANJA_HORARIA"] = df["HORA_INGRESO"].dt.hour.astype(str).str.zfill(2) + ":00-" +                        (df["HORA_INGRESO"].dt.hour + 1).astype(str).str.zfill(2) + ":00"
```

- Convierte la columna `HORA_INGRESO` a hora real.
- Extrae el minuto exacto de ingreso.
- Crea una nueva columna `FRANJA_HORARIA`, útil para agrupar datos por hora en los reportes (ej: "08:00-09:00").

---

### 3. ⏱️ Cálculo de tiempo en segundos

```python
def tiempo_a_segundos(tiempo_str):
    h, m, s = map(int, str(tiempo_str).split(":"))
    return h * 3600 + m * 60 + s

df["SEGUNDOS_ATENCION"] = df["TIEMPO_ATENCION"].apply(tiempo_a_segundos)
df["SEGUNDOS_ESPERA"] = df["TIEMPO_ESPERA"].apply(tiempo_a_segundos)
df["TIEMPO_TOTAL"] = df["SEGUNDOS_ATENCION"] + df["SEGUNDOS_ESPERA"]
```

- Convierte el tiempo (`HH:MM:SS`) en segundos para poder sumar, promediar y comparar fácilmente.
- Calcula el **tiempo total de atención** por cada registro.

---

### 4. 📊 Clasificación por rango de tiempo

```python
def clasificar_tiempo(segundos):
    if segundos is None:
        return "desconocido"
    elif segundos <= 30:
        return "corta"
    elif segundos <= 120:
        return "media"
    else:
        return "larga"

df["RANGO_ATENCION"] = df["SEGUNDOS_ATENCION"].apply(clasificar_tiempo)
df["RANGO_ESPERA"] = df["SEGUNDOS_ESPERA"].apply(clasificar_tiempo)
```

- Crea columnas categóricas que agrupan los tiempos en:
  - `corta`: hasta 30 segundos
  - `media`: hasta 120 segundos
  - `larga`: más de 2 minutos
- Esto permite hacer **gráficos por duración de atención o espera** fácilmente.

---

## 🎯 ¿Por qué se hace todo esto?

Estas transformaciones:

- Enriquecen los datos con columnas clave para análisis temporal y de eficiencia.
- Preparan los datos para cargarlos a PostgreSQL siguiendo un modelo estrella.
- Facilitan la creación de reportes dinámicos y paneles en Power BI.

---

📂 Al final, el DataFrame resultante tiene columnas adicionales que permiten más análisis, agrupaciones y filtros avanzados.
