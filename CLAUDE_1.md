# Modelo de apuestas de fútbol

## Objetivo
Proyecto personal para estimar probabilidades de resultados de fútbol y compararlas con las cuotas de las casas de apuestas, buscando apuestas con valor esperado positivo. A futuro también servirá como portafolio de análisis de datos deportivos.

El objetivo NO es acertar más resultados, sino encontrar cuotas donde la casa subestima una probabilidad (incluido su margen).

## Stack
- Python 3.11+, entorno virtual en `.venv`
- pandas, numpy, scipy, sqlite3 (más adelante quizás statsmodels / scikit-learn)
- Base de datos local SQLite en `data/`
- Configuración en `config.yaml` (ligas, temporadas, parámetros del modelo)
- Claves de API en `.env` (nunca en el código ni en git)

## Estructura
```
data/          # CSVs y base SQLite (ignorado por git)
src/ingesta/   # descarga y carga de datos
src/modelo/    # modelos de probabilidad
src/evaluacion/# backtesting y métricas
src/bot/       # bot de Telegram (fase futura)
notebooks/     # exploración
tests/
```

## Fuentes de datos
- football-data.co.uk: resultados históricos y cuotas, incluidas las de cierre de Pinnacle (PSCH, PSCD, PSCA).
- Futuro: API-Football (alineaciones, estadísticas por jugador, liga chilena), The Odds API (cuotas en vivo de varias casas), Open-Meteo (clima).
- No hacer scraping de sitios de casas de apuestas.

## Ligas
- Empezar con la Premier League (código E0). Liga chilena después.
- La liga debe ser siempre un parámetro de configuración, nunca algo fijo en el código.

## Conceptos clave
- Probabilidad implícita = 1 / cuota decimal.
- Las probabilidades implícitas de una casa suman más de 100%; el exceso es el margen. Para obtener probabilidades "justas", quitar el margen (normalización proporcional como mínimo).
- La cuota de cierre de Pinnacle es la mejor referencia de la probabilidad real. Batir la cuota de cierre de forma consistente es la principal señal de que el modelo sirve.
- Valor esperado (EV) = probabilidad_modelo × cuota − 1. Solo apostar con EV positivo por sobre un umbral.

## Reglas de modelado
- Validación siempre temporal: entrenar con el pasado y probar con el futuro. Nunca mezclar partidos futuros en el entrenamiento (sin fuga de datos).
- Evitar el sobreajuste: empezar con pocas variables fuertes y agregar variables nuevas de una en una, quedándose solo con las que mejoran métricas en datos de prueba.
- Métricas a reportar: log-loss, Brier score, precisión, ROI simulado, número de apuestas y comparación contra la cuota de cierre.
- Ser escéptico con resultados demasiado buenos: probablemente hay un error o fuga de datos.

## Convenciones
- Comentarios, README y mensajes al usuario en español; nombres de variables y funciones en español o inglés, pero consistentes.
- Funciones pequeñas y testeables; scripts ejecutables desde la terminal.
- Explicar brevemente cada decisión estadística, porque el dueño del proyecto está aprendiendo modelado deportivo.

## Fases
1. Datos históricos + modelo de Poisson + backtesting contra cuotas de cierre.
2. Cuotas en vivo y alineaciones, con alertas de valor.
3. Bot de Telegram para consultas.
4. Variables extra (balón parado, clima, altura, noticias), probadas de una en una.
