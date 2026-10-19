# Predicción de resultados NFL 2026

Modelo de machine learning en Jupyter Notebook que pronostica, para cada partido de la temporada regular de la NFL:

- **Ganador** (probabilidad de victoria local/visitante) con un `RandomForestClassifier`.
- **Margen de victoria** y **total de puntos** con dos `RandomForestRegressor`, de los que se deriva el marcador estimado.
- **Over/Under**: compara el total estimado contra la línea de Vegas.

El marcador siempre se ajusta para coincidir con el ganador del clasificador.

## Datos y variables

- Histórico de calendarios/resultados 2016-2025 (CSV local) y calendario 2026 vía [`nflreadpy`](https://github.com/nflverse/nflreadpy).
- Play-by-play, estadísticas de equipo, de mariscales (QB) y reportes de lesiones, también vía `nflreadpy`.
- Clima del partido: pronóstico automático con [Open-Meteo](https://open-meteo.com/) (gratis, sin API key).

Features principales: rating **Elo**, forma reciente (puntos a favor/en contra, % de victorias), **EPA** ofensivo/defensivo (pase y carrera), 3ra oportunidad, zona roja, diferencial de balones perdidos y capturas, descanso, **lesiones** (con mayor peso al QB), EPA del QB titular, clima (temperatura, viento, techo/domo) y las líneas de Vegas (spread y total).

## Validación

Validación **walk-forward** por temporada: se entrena con las temporadas anteriores y se evalúa en la siguiente (sin mezclar al azar, para evitar fuga de datos).

| Temporada | Accuracy | Log loss | MAE local | MAE visitante |
|-----------|----------|----------|-----------|---------------|
| 2022 | 0.649 | 0.623 | 7.05 | 7.09 |
| 2023 | 0.651 | 0.641 | 7.50 | 7.31 |
| 2024 | 0.695 | 0.594 | 7.46 | 6.85 |
| 2025 | 0.636 | 0.617 | 7.28 | 7.25 |
| **Promedio** | **0.658** | **0.619** | **7.33** | **7.12** |

> **Nota:** el modelo usa el spread y el total de Vegas como features, por lo que parte de su desempeño proviene de la información del mercado. Para interpretar el 65.8 % conviene compararlo con la línea base de "gana el favorito de Vegas".

## Cómo ejecutarlo

```bash
git clone https://github.com/TU_USUARIO/nfl-prediccion.git
cd nfl-prediccion
python -m venv .venv && source .venv/bin/activate   # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/modelo_nfl_2026.ipynb
```

1. Coloca el histórico `nfl_datos_2016-2025.csv` en la carpeta `Datos/` (no se incluye en el repo).
2. Para predecir otra semana, cambia `PREDICT_WEEK` en la celda de configuración.
3. La primera corrida descarga el play-by-play de 11 temporadas (varios minutos); después usa la caché en `cache_nfl/`.

## Tecnologías

Python, pandas, NumPy, scikit-learn, nflreadpy, Open-Meteo.

## Aviso

Proyecto con fines educativos y de análisis. No constituye asesoría de apuestas.
