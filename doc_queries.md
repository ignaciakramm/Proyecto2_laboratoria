# 📊 Documentación de Queries SQL

Este documento recopila las consultas realizadas sobre las tablas de la base `spotify2023_base`, con explicaciones de su propósito y funcionamiento.  

---

## 🎯 Tabla: `track_in_competition`

### 1. Contar valores nulos en `in_shazam_charts`
```sql
SELECT COUNT(*) AS nulos_in_shazam
FROM `spotify2023_base.track_in_competition`
WHERE in_shazam_charts IS NULL;
```

**Función:**  
Cuenta cuántas filas tienen `NULL` en la columna `in_shazam_charts`.  
- `COUNT(*)` cuenta todas las filas.  
- `WHERE in_shazam_charts IS NULL` filtra solo las filas nulas.  

---

### 2. Crear tabla limpia con reemplazo de nulos
```sql
CREATE OR REPLACE TABLE `spotify2023_base.track_in_competition_limpia` AS
SELECT
    *,
    IFNULL(in_shazam_charts, 0) AS in_shazam_charts_limpio
FROM `spotify2023_base.track_in_competition`;
```

**Función:**  
Genera una tabla nueva (`track_in_competition_limpia`) donde:  
- `*` mantiene todas las columnas originales.  
- `IFNULL(in_shazam_charts, 0)` reemplaza valores `NULL` de la columna por `0`.  
- Se añade la columna `in_shazam_charts_limpio` con los datos corregidos.  

---

## 🎯 Queries genéricas para cualquier tabla

### 3. Contar nulos en varias columnas
```sql
SELECT 
  COUNT(*) - COUNT(columna1) AS columna1_nulls,
  COUNT(*) - COUNT(columna2) AS columna2_nulls,
  COUNT(*) - COUNT(columna3) AS columna3_nulls
FROM `mi_base.mi_tabla`;
```

**Función:**  
Obtiene la cantidad de `NULL` por columna en una tabla:  
- `COUNT(columna)` solo cuenta valores **no nulos**.  
- `COUNT(*) - COUNT(columna)` devuelve el número de `NULL`.  

---

### 4. Buscar duplicados por columna clave
```sql
SELECT track_id, COUNT(*) AS repeticiones
FROM `mi_base.mi_tabla`
GROUP BY track_id
HAVING COUNT(*) > 1;
```

**Función:**  
Muestra los valores de `track_id` que están repetidos en la tabla.  
- `GROUP BY track_id` agrupa filas con el mismo valor.  
- `HAVING COUNT(*) > 1` devuelve solo los casos con duplicados.  

---

## 🎯 Tabla combinada de varias tablas

### 5. Reemplazo de nulos y eliminación de duplicados (misma estructura)
```sql
CREATE OR REPLACE TABLE `mi_base.tabla_final_limpia` AS
SELECT DISTINCT *
FROM (
  SELECT
    track_id,
    IFNULL(columna1, 0) AS columna1,
    IFNULL(columna2, 'desconocido') AS columna2,
    IFNULL(columna3, 0) AS columna3
  FROM `mi_base.tabla1`

  UNION ALL

  SELECT
    track_id,
    IFNULL(columna1, 0) AS columna1,
    IFNULL(columna2, 'desconocido') AS columna2,
    IFNULL(columna3, 0) AS columna3
  FROM `mi_base.tabla2`

  UNION ALL

  SELECT
    track_id,
    IFNULL(columna1, 0) AS columna1,
    IFNULL(columna2, 'desconocido') AS columna2,
    IFNULL(columna3, 0) AS columna3
  FROM `mi_base.tabla3`
);
```

**Función:**  
Crea una tabla final (`tabla_final_limpia`) donde:  
- Se combinan las tres tablas (`tabla1`, `tabla2`, `tabla3`) con `UNION ALL`.  
- Los valores `NULL` se reemplazan con un valor válido (`0` o `'desconocido'`).  
- `SELECT DISTINCT *` elimina filas duplicadas después de unir.  

---

### 6. Eliminar filas con nulos y duplicados
```sql
CREATE OR REPLACE TABLE `mi_base.tabla_final_limpia` AS
SELECT DISTINCT *
FROM (
  SELECT *
  FROM `mi_base.tabla1`
  WHERE columna1 IS NOT NULL AND columna2 IS NOT NULL AND columna3 IS NOT NULL
  
  UNION ALL
  
  SELECT *
  FROM `mi_base.tabla2`
  WHERE columna1 IS NOT NULL AND columna2 IS NOT NULL AND columna3 IS NOT NULL
  
  UNION ALL
  
  SELECT *
  FROM `mi_base.tabla3`
  WHERE columna1 IS NOT NULL AND columna2 IS NOT NULL AND columna3 IS NOT NULL
);
```

**Función:**  
Crea una tabla combinada (`tabla_final_limpia`) donde:  
- Se eliminan filas que tengan `NULL` en cualquiera de las columnas seleccionadas.  
- Se combinan las tres tablas con `UNION ALL`.  
- `SELECT DISTINCT *` elimina filas duplicadas en el resultado final.  

