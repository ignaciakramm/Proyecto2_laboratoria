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

--🔎 Identificación y Tratamiento de Valores Atípicos en Spotify Dataset

Este trabajo se centra en la limpieza y exploración de valores atípicos en distintas tablas de un dataset de Spotify y plataformas de competencia.

1. Tablas utilizadas

track_in_spotify_clean → Información de canciones en Spotify.

technical_info_clean → Información técnica de cada track (BPM, energía, valencia, etc.).

competition_info_clean → Presencia de canciones en Apple, Deezer y Shazam.

2. Variables categóricas analizadas

track_name y artist_s__name:

Se usaron expresiones LIKE y REGEXP para detectar inconsistencias de texto, como:

Presencia de "feat.", "ft.", etc.

Caracteres especiales innecesarios (@, #, !, etc.).

Uso indebido de mayúsculas/minúsculas.

Ejemplo:

SELECT artist_s__name
FROM `spotify2023_base.track_in_spotify_clean`
WHERE artist_s__name LIKE '%feat%' OR artist_s__name LIKE '%ft.%';


Para limpieza:

SELECT 
  artist_s__name,
  REGEXP_REPLACE(artist_s__name, r'[^a-zA-Z0-9, ]','') AS artist_name_limpio
FROM `spotify2023_base.track_in_spotify_clean`;

3. Variables numéricas analizadas

Se calcularon mínimos, máximos y promedios con el objetivo de identificar outliers y validar rangos.

Ejemplo de consulta:
SELECT
  MIN(streams) AS min_streams,
  MAX(streams) AS max_streams,
  AVG(streams) AS avg_streams
FROM `spotify2023_base.track_in_spotify_clean`;

Resultados clave:

Streams: Sirve para ver la canción menos y más reproducida, y la media general.

BPM: Valores fuera de rango (ej: mayores a 300) se consideran atípicos.

Danceability, Energy, Valence, etc.: MIN y MAX deben estar entre 0 y 100. Valores fuera de este rango son errores.

Competition (Apple, Deezer, Shazam):

MIN = 0 es esperado (canciones sin presencia).

MAX ayuda a encontrar las canciones más populares.

AVG indica el promedio de exposición en cada plataforma.

4. Principales hallazgos

Se detectó un registro en streams con valores de tokens técnicos en lugar de números → fue reemplazado por 0.

En artist_s__name, se identificó que algunos artistas estaban listados como cadenas separadas por comas. Lo correcto sería dividirlos en múltiples filas en lugar de eliminar la coma.

Variables como mode (solo contiene “major” y “minor”) no requieren estandarización, pero se verificó su consistencia con REGEXP_REPLACE.

Las métricas de MIN, MAX y AVG permiten identificar canciones con desempeño extremo y posibles errores de carga en las tablas.
