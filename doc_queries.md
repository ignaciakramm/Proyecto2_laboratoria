# Documentación de Queries - Base `spotify2023`

Este documento reúne todas las consultas SQL realizadas, organizadas por tabla, con sus explicaciones y funciones.  
Se incluyen únicamente referencias a la base **spotify2023**.

---

## Tabla: `spotify2023.track_in`

### Conteo de nulos
```sql
# Conteo de nulos en track_in
# Resultado: 0 nulos
```

**Función:** Verificar si existen valores nulos en la tabla. El resultado mostró que no hay nulos.

### Duplicados en columnas principales
```sql
-- Duplicados en track_id
SELECT track_id, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY track_id
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en track_name
SELECT track_name, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY track_name
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en artist_s__name
SELECT artist_s__name, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY artist_s__name
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en artist_count
SELECT artist_count, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY artist_count
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en released_year
SELECT released_year, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY released_year
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en released_month
SELECT released_month, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY released_month
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en released_day
SELECT released_day, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY released_day
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en in_spotify_playlists
SELECT in_spotify_playlists, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY in_spotify_playlists
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en in_spotify_charts
SELECT in_spotify_charts, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY in_spotify_charts
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en streams
SELECT streams, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY streams
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;
```

**Función:** Identificar duplicados en cada columna relevante de la tabla.

### Duplicados combinados (track_name + artista)
```sql
SELECT track_name, artist_s__name, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in`
GROUP BY track_name, artist_s__name
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;
```

**Función:** Detectar duplicados considerando la combinación de nombre de canción + artista.

### Cruce de datos de duplicados
```sql
SELECT *
FROM `spotify2023.track_in`
WHERE CONCAT(track_name, ' - ', artist_s__name) IN (
  SELECT CONCAT(track_name, ' - ', artist_s__name)
  FROM `spotify2023.track_in`
  GROUP BY track_name, artist_s__name
  HAVING COUNT(*) > 1
)
ORDER BY track_name, artist_s__name;
```

**Función:** Mostrar los registros duplicados para decidir si deben eliminarse o homologarse.

---

## Tabla: `spotify2023.track_in_compe`

### Conteo de nulos
```sql
SELECT
  COUNT(*) AS total_filas,
  COUNTIF(track_id IS NULL) AS nulos_track_id,
  COUNTIF(in_apple_playlists IS NULL) AS nulos_in_apple_playlists,
  COUNTIF(in_apple_charts IS NULL) AS nulos_in_apple_charts,
  COUNTIF(in_deezer_playlists IS NULL) AS nulos_in_deezer_playlists,
  COUNTIF(in_deezer_charts IS NULL) AS nulos_in_deezer_charts,
  COUNTIF(in_shazam_charts IS NULL) AS nulos_in_shazam_charts
FROM `spotify2023.track_in_compe`;
# Resultado: 50 nulos en in_shazam_charts
```

**Función:** Contabilizar valores nulos por columna. Se detectaron 50 nulos en `in_shazam_charts`.

### Reemplazo de nulos (vista limpia)
```sql
CREATE OR REPLACE VIEW `spotify2023.track_in_compe_clean` AS
SELECT
  track_id,
  IFNULL(in_apple_playlists, 0)   AS in_apple_playlists,
  IFNULL(in_apple_charts, 0)      AS in_apple_charts,
  IFNULL(in_deezer_playlists, 0)  AS in_deezer_playlists,
  IFNULL(in_deezer_charts, 0)     AS in_deezer_charts,
  IFNULL(in_shazam_charts, 0)     AS in_shazam_charts
FROM `spotify2023.track_in_compe`;
```

**Función:** Crear una vista limpia reemplazando los nulos por `0`.

### Duplicados en columnas principales
```sql
-- Duplicados en track_id
SELECT track_id, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in_compe`
GROUP BY track_id
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en in_apple_playlists
SELECT in_apple_playlists, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in_compe`
GROUP BY in_apple_playlists
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en in_apple_charts
SELECT in_apple_charts, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in_compe`
GROUP BY in_apple_charts
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en in_deezer_playlists
SELECT in_deezer_playlists, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in_compe`
GROUP BY in_deezer_playlists
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en in_deezer_charts
SELECT in_deezer_charts, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in_compe`
GROUP BY in_deezer_charts
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en in_shazam_charts
SELECT in_shazam_charts, COUNT(*) AS veces_repetido
FROM `spotify2023.track_in_compe`
GROUP BY in_shazam_charts
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;
```

**Función:** Identificar duplicados en todas las columnas relevantes.

---

## Tabla: `spotify2023.technical_info`

### Conteo de nulos
```sql
SELECT
  COUNT(*) AS total_filas,
  COUNTIF(track_id IS NULL) AS nulos_track_id,
  COUNTIF(bpm IS NULL) AS nulos_bpm,
  COUNTIF(`key` IS NULL) AS nulos_key,
  COUNTIF(`mode` IS NULL) AS nulos_mode,
  COUNTIF(`danceability_%` IS NULL) AS nulos_danceability,
  COUNTIF(`valence_%` IS NULL) AS nulos_valence,
  COUNTIF(`energy_%` IS NULL) AS nulos_energy,
  COUNTIF(`acousticness_%` IS NULL) AS nulos_acousticness,
  COUNTIF(`instrumentalness_%` IS NULL) AS nulos_instrumentalness,
  COUNTIF(`liveness_%` IS NULL) AS nulos_liveness,
  COUNTIF(`speechiness_%` IS NULL) AS nulos_speechiness
FROM `spotify2023.technical_info`;
# Resultado: 0 nulos
```

**Función:** Validar si existen valores nulos en las columnas principales. Resultado: no se encontraron nulos.

### Duplicados en columnas principales
```sql
-- Duplicados en track_id
SELECT track_id, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY track_id
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en bpm
SELECT bpm, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY bpm
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en key
SELECT `key`, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY `key`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en mode
SELECT `mode`, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY `mode`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en danceability_%
SELECT `danceability_%`, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY `danceability_%`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en valence_%
SELECT `valence_%`, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY `valence_%`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en energy_%
SELECT `energy_%`, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY `energy_%`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en acousticness_%
SELECT `acousticness_%`, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY `acousticness_%`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en instrumentalness_%
SELECT `instrumentalness_%`, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY `instrumentalness_%`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en liveness_%
SELECT `liveness_%`, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY `liveness_%`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en speechiness_%
SELECT `speechiness_%`, COUNT(*) AS veces_repetido
FROM `spotify2023.technical_info`
GROUP BY `speechiness_%`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;
```

**Función:** Identificar valores duplicados en las columnas principales de la tabla.

# Limpieza y detección de valores atípicos en Spotify

## 1. Limpieza de nombres de artistas

```sql
-- Limpieza de caracteres no deseados en artist_s__name
SELECT 
  artist_s__name,
  REGEXP_REPLACE(artist_s__name, r'[^a-zA-Z0-9,]', ' ') AS artist_name_limpio
FROM `spotify2023_base.track_in_spotify_clean`;
-- reemplaza cualquier carácter especial (acentos, paréntesis, comas, puntos, guiones, etc.) por un espacio. NOMBRE DE CANCIÓN
SELECT track_name, 
REGEXP_REPLACE(track_name, r'[^a-zA-Z0-9]',' ') AS track_name_limpio
FROM `spotify2023_base.track_in_spotify_clean`;

```

**Función:** Mantener las comas y eliminar caracteres extraños de los nombres de artistas para estandarizar la columna.

---

## 2. Normalización de columna `mode`

```sql
-- Normalización de valores en "mode"
SELECT 
  mode,
  REGEXP_REPLACE(LOWER(mode), r'[^a-z]', '') AS mode_estandarizado
FROM `spotify2023_base.technical_info_clean`;
```

**Función:** Unificar valores de "mode" en categorías válidas (major/minor), evitando inconsistencias de mayúsculas o caracteres extra.

---

## 3. Estadísticas descriptivas para detección de outliers

```sql
-- Ejemplo con in_apple_playlists
SELECT 
  MIN(in_apple_playlists) AS min_val, 
  MAX(in_apple_playlists) AS max_val, 
  AVG(in_apple_playlists) AS avg_val
FROM `spotify2023_base.competition_info_clean`;
```

**Función:** Calcular mínimo, máximo y promedio para detectar valores nulos, extremos o atípicos. Repetir para otras métricas (in_apple_charts, in_deezer_playlists, in_deezer_charts, in_shazam_charts).

---

## 4. Duplicados en columnas principales

```sql
-- Duplicados en track_id
SELECT track_id, COUNT(*) AS veces_repetido
FROM `spotify2023_base.technical_info`
GROUP BY track_id
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en bpm
SELECT bpm, COUNT(*) AS veces_repetido
FROM `spotify2023_base.technical_info`
GROUP BY bpm
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en key
SELECT `key`, COUNT(*) AS veces_repetido
FROM `spotify2023_base.technical_info`
GROUP BY `key`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;

-- Duplicados en mode
SELECT `mode`, COUNT(*) AS veces_repetido
FROM `spotify2023_base.technical_info`
GROUP BY `mode`
HAVING COUNT(*) > 1
ORDER BY veces_repetido DESC;
```

**Función:** Identificar valores duplicados para validar la consistencia de los datos antes de análisis adicionales.

---

## 5. Estadísticas de columnas enteras en track_in_spotify_clean

```sql
SELECT
  MIN(first_release_day),
  MAX(first_release_day),
  AVG(first_release_day)
FROM `spotify2023_base.track_in_spotify_clean`;
```

**Función:** Obtener rangos y promedio para columnas numéricas, útil para detección de valores atípicos o errores de captura.

---

## 6. Estadísticas de columnas enteras en technical_info_clean

```sql
SELECT
  MIN(bpm), MAX(bpm), AVG(bpm),
  MIN(`danceability_%`), MAX(`danceability_%`), AVG(`danceability_%`),
  MIN(`valence_%`), MAX(`valence_%`), AVG(`valence_%`),
  MIN(`energy_%`), MAX(`energy_%`), AVG(`energy_%`),
  MIN(`acousticness_%`), MAX(`acousticness_%`), AVG(`acousticness_%`),
  MIN(`instrumentalness_%`), MAX(`instrumentalness_%`), AVG(`instrumentalness_%`),
  MIN(`liveness_%`), MAX(`liveness_%`), AVG(`liveness_%`),
  MIN(`speechiness_%`), MAX(`speechiness_%`), AVG(`speechiness_%`)
FROM `spotify2023_base.technical_info_clean`;
```

**Función:** Revisar rangos y promedios para variables técnicas de las canciones, ayudando a identificar posibles outliers.

---

## 7. Estadísticas de columnas enteras en competition_info_clean

```sql
SELECT
  MIN(in_apple_playlists), MAX(in_apple_playlists), AVG(in_apple_playlists),
  MIN(in_apple_charts), MAX(in_apple_charts), AVG(in_apple_charts),
  MIN(in_deezer_playlists), MAX(in_deezer_playlists), AVG(in_deezer_playlists),
  MIN(in_deezer_charts), MAX(in_deezer_charts), AVG(in_deezer_charts),
  MIN(in_shazam_charts), MAX(in_shazam_charts), AVG(in_shazam_charts)
FROM `spotify2023_base.competition_info_clean`;
```

**Función:** Evaluar el comportamiento de métricas de popularidad en distintas plataformas, detectando posibles registros atípicos o inconsistentes.

---

**Resumen:** Estas consultas permiten:

* Limpiar y estandarizar variables categóricas (nombres de artistas, modo).
* Detectar duplicados en columnas clave.
* Revisar rangos y promedios para columnas numéricas, facilitando la identificación de valores atípicos.
* Mejorar la consistencia y calidad del dataset para análisis posteriores.
