# RESULTADOS_PRINCIPALES — Guía de Exploración del Repositorio

**Proyecto:** Evaluación de Inversión en Empresas de Inteligencia Artificial  
**Autor:** Joan Valderrama Inoñan  
**Contexto:** Caso práctico — Proceso de selección Macroconsult (Sistema de Información)  
**Fecha:** Junio 2026

---

## Descripción general

Este repositorio contiene todos los insumos y resultados del análisis cuantitativo desarrollado para evaluar la conveniencia de invertir en acciones de empresas vinculadas a la Inteligencia Artificial. El entregable final es la presentación en PowerPoint de máximo 6 diapositivas. Los demás archivos documentan el sustento metodológico y econométrico detrás de cada afirmación de la presentación.

El análisis se apoya en tres pilares:

1. **Análisis de cartera** (optimización de Markowitz con datos semanales de acciones IA)
2. **Modelo GARCH + métricas de riesgo** (volatilidad condicional, VaR dinámico y Sharpe Ratio en EViews)
3. **Modelo Markov Switching** (estados económicos a partir del ciclo del PIB y crédito no financiero, en MATLAB)

---

## Estructura de carpetas

```
RESULTADOS_PRINCIPALES/
│
├── CASO_PRACTICO_JOAN_VALDERRAMA_INOÑAN.pptx   ← Entregable final
│
├── LITERATURA: Investigaciones previas y análisis sectorial
├── ANÁLISIS_CARTERA/                            ← Optimización de portafolio
│   ├── DATOS_SEMANALES_ACCIONES_IA.xlsx
│   ├── DATOS_DAMODARAN_EQUITY_RISK_PREMIUM.xlsx
│   └── PORTAFOLIO_MARKOWITZ.xlsx
│
└── ANALISIS_ECONOMETRICO/
    │
    ├── MODELO_GARCH_MÉTRICAS_RIESGO_VaR/        ← Modelos de volatilidad (EViews)
    │   └── MODELOS_ARMA_ESTIMADOS/
    │       ├── datos_cargados.wf1
    │       ├── codigos_series_creadas.prg
    │       ├── program_arma_panel_ia_v3_final.evsettings
    │       ├── program_arma_panel_ia_v4_parsimonioso.evsettings
    │       ├── garch_var_sharpe_final.prg
    │       ├── garch_var_sharpe_final.~rg
    │       ├── estados_economia.evsettings
    │       └── indicadores_riesgo_post_estimación.evsettings
    │
    └── MODELO_MARKOV_SWITCHING_ESTADOS_ECONOMIA/ ← Regímenes económicos (MATLAB)
        └── ms_ctsnf_pbi/
            ├── main_MS_cycle.m                  ← Script principal PIB
            ├── main_MS_cycle_CTSNF.m            ← Script principal crédito
            ├── README.md                         ← Instrucciones técnicas detalladas
            ├── README_CTSNF.md
            ├── data/                             ← Series GDPC1 y CTSNF
            ├── functions/                        ← Funciones MATLAB modulares
            ├── output_pbi/                       ← Figuras generadas (PIB)
            ├── output_ctsnf/                     ← Figuras generadas (crédito)
            ├── latex_pbi/                        ← Reporte LaTeX (PIB)
            └── latex_ctsnf/                      ← Reporte LaTeX (crédito)
```

---

## Entregable final

**Archivo:** `CASO_PRACTICO_JOAN_VALDERRAMA_INOÑAN.pptx`

Presentación de hasta 6 diapositivas (incluida la de título) que sustenta la recomendación de inversión en acciones de empresas de IA para un inversionista externo. Cada diapositiva se respalda con los análisis cuantitativos de las carpetas descritas a continuación.

---

## ANÁLISIS_CARTERA

**Herramienta:** Microsoft Excel  
**Propósito:** Construir y optimizar un portafolio de acciones de empresas de IA bajo el marco de Media-Varianza de Markowitz.

**Activos analizados:** NVDA, MSFT, GOOGL, META, TSM, CRM

### Archivos

`DATOS_SEMANALES_ACCIONES_IA.xlsx` contiene los precios semanales de cierre de los seis activos tecnológicos. Es la fuente de datos primaria para calcular retornos logarítmicos, covarianzas y métricas de portafolio.

`DATOS_DAMODARAN_EQUITY_RISK_PREMIUM.xlsx` contiene el Equity Risk Premium (ERP) por país y sector estimado por Damodaran (NYU Stern). Fue descargado desde `https://pages.stern.nyu.edu/~adamodar/`. Se usa como insumo para la tasa de descuento y la comparación de rendimientos ajustados por riesgo.

`PORTAFOLIO_MARKOWITZ.xlsx` contiene los resultados de la optimización: frontera eficiente, portafolio de mínima varianza, portafolio de máximo Sharpe Ratio, pesos óptimos y métricas de riesgo-retorno consolidadas.

---

## ANALISIS_ECONOMETRICO / MODELO_GARCH_MÉTRICAS_RIESGO_VaR

**Herramienta:** EViews (versión con soporte para modelos GARCH-t)  
**Carpeta de trabajo:** `MODELOS_ARMA_ESTIMADOS/`  
**Propósito:** Estimar la volatilidad condicional de cada acción, calcular el VaR dinámico bajo distribución t-Student y el Sharpe Ratio condicional anualizado.

### Flujo de trabajo

El análisis sigue el siguiente orden de ejecución dentro de EViews:

**Paso 1 — Cargar datos** (`datos_cargados.wf1`): Workfile de EViews con las series de precios semanales de NVDA, MSFT, GOOGL, META, TSM y CRM, más los benchmarks S&P 500 y NASDAQ 100. Abrir este archivo para acceder al entorno de trabajo.

**Paso 2 — Crear retornos** (`codigos_series_creadas.prg`): Programa que calcula los retornos logarítmicos continuos `r_t = ln(P_t / P_{t-1})` para cada activo. También genera la volatilidad anualizada y las métricas de VaR y Sharpe utilizando los parámetros de los modelos GARCH estimados.

**Paso 3 — Estimar ecuaciones de media ARMA** (`program_arma_panel_ia_v3_final.evsettings` y `v4_parsimonioso.evsettings`): Configuraciones de los modelos de ecuación de media para cada activo. La especificación parsimoniosa (v4) selecciona el ARMA más adecuado para que los residuos queden libres de autocorrelación antes de aplicar el test ARCH-LM.

**Paso 4 — Estimar GARCH y calcular VaR** (`garch_var_sharpe_final.prg`): Programa principal. Ejecuta los siguientes bloques en secuencia:
- Cálculo de retornos logarítmicos para los seis activos
- Estimación de ecuaciones de media por OLS
- Prueba ARCH-LM (Engle, 1982) con rezagos 1 a 4
- Selección del modelo GARCH(p,q)-t por criterio de Schwarz
- VaR dinámico semanal al 95% y 99% (distribución t-Student con grados de libertad específicos por activo: NVDA=14.37, GOOGL=19.02, META=6.36, MSFT=13.60)
- Backtesting: Kupiec (1995) y Christoffersen (1998)
- Sharpe Ratio condicional anualizado (escala por √52)

**Paso 5 — Indicadores post-estimación** (`indicadores_riesgo_post_estimación.evsettings`): Tablas y gráficos adicionales de riesgo derivados de los modelos GARCH estimados.

**Paso 6 — Estados de economía** (`estados_economia.evsettings`): Clasificación de los períodos de muestra según el estado del mercado, complementando el análisis de regímenes del modelo Markov Switching.

### Notas técnicas

El archivo `garch_var_sharpe_final.~rg` es una copia de respaldo automática generada por EViews del programa principal (extensión `~rg`). Puede ignorarse durante la exploración normal. Las carpetas `*_Snapshots/` contienen instantáneas de versiones anteriores guardadas automáticamente por EViews, útiles para recuperar estados previos del análisis.

---

## ANALISIS_ECONOMETRICO / MODELO_MARKOV_SWITCHING_ESTADOS_ECONOMIA

**Herramienta:** MATLAB R2019b o superior (requiere Statistics and Machine Learning Toolbox)  
**Carpeta de trabajo:** `ms_ctsnf_pbi/`  
**Propósito:** Identificar los regímenes o estados de la economía (recesión, neutral, expansión) mediante un modelo Markov Switching MS-MH(3) aplicado al ciclo del PIB real de EE.UU. y al ciclo del crédito al sector no financiero.

### Flujo de trabajo

**Para el análisis del ciclo del PIB:**

```matlab
cd('ruta/a/ms_ctsnf_pbi')
main_MS_cycle
```

El script detecta automáticamente `data/gdp_usa.csv` (serie GDPC1 de FRED, 1947-Q1 a 2026-Q1, 317 observaciones) y ejecuta el pipeline completo sin configuración adicional.

**Para el análisis del crédito al sector no financiero (CTSNF):**

```matlab
cd('ruta/a/ms_ctsnf_pbi')
% Paso previo: generar el CSV desde el Excel
run('data/convert_ctsnf_xlsx.m')
% Luego ejecutar el pipeline
main_MS_cycle_CTSNF
```

### Estructura de subcarpetas

`data/` almacena las series de datos. Contiene `gdp_usa.csv` (PIB real GDPC1), `GDPC1.xlsx` (descarga original de FRED), `ctsnf_real.csv` (serie de crédito no financiero generada por el script de conversión) y `COLOQUE_DATOS_AQUI.txt` con instrucciones para actualizar las series.

`functions/` contiene las funciones MATLAB modulares del pipeline: `cf_filter_asymmetric.m` (filtro Christiano-Fitzgerald asimétrico para extraer el ciclo), `estimate_markov_switching.m` (estimación MS-MH(K) por algoritmo EM), `identify_regimes.m` (clasificación de regímenes por media), `plot_smoothed_probs.m` y `plot_regime_classification.m` (visualizaciones), y `generate_latex_report.m` (generador del reporte técnico en LaTeX).

`output_pbi/` y `output_ctsnf/` contienen las figuras PDF generadas automáticamente al correr el pipeline: `fig1_gdp_cycle.pdf` (PIB y ciclo CF), `fig2_smoothed_probs.pdf` (probabilidades suavizadas por régimen), `fig3_transition_matrix.pdf` (heatmap de la matriz de transición) y `fig4_regime_classification.pdf` (clasificación histórica de regímenes).

`latex_pbi/` y `latex_ctsnf/` contienen los archivos `reporte_MS_ciclo.tex` con el reporte técnico completo generado automáticamente. Para compilarlo a PDF se requiere una instalación de LaTeX (`pdflatex`) en el sistema.

### Parámetros configurables

Los principales parámetros del modelo se encuentran en el bloque `params` al inicio de `main_MS_cycle.m`. Los valores por defecto son: 3 regímenes (`states = 3`), ciclo CF de 6 a 32 trimestres (`cf_pl = 6`, `cf_pu = 32`), máximo 2000 iteraciones del algoritmo EM (`maxiter = 2000`) y semilla 42 para reproducibilidad (`seed = 42`).

---

## Software requerido

| Módulo | Herramienta | Versión mínima |
|---|---|---|
| Análisis de cartera | Microsoft Excel | 2016 o superior |
| Modelos GARCH / ARMA / VaR | EViews | 10 o superior |
| Modelo Markov Switching | MATLAB | R2019b + Statistics Toolbox |
| Reporte técnico (opcional) | LaTeX (pdflatex) | TeX Live 2020 o MiKTeX |

---

## Referencias metodológicas principales

- Campbell, Lo & MacKinlay (1997). *The Econometrics of Financial Markets*. Princeton UP.
- Engle, R.F. (1982). Autoregressive Conditional Heteroscedasticity. *Econometrica*, 50(4).
- Hamilton, J.D. (1989). A New Approach to the Economic Analysis of Nonstationary Time Series. *Econometrica*, 57(2).
- Kim, C.J. (1994). Dynamic Linear Models with Markov-Switching. *Journal of Econometrics*, 60.
- Christiano & Fitzgerald (2003). The Band Pass Filter. *International Economic Review*, 44(2).
- McNeil, Frey & Embrechts (2005). *Quantitative Risk Management*. Princeton UP.
- Markowitz, H. (1952). Portfolio Selection. *Journal of Finance*, 7(1).
- Kupiec, P. (1995). Techniques for Verifying the Accuracy of Risk Measurement Models. *Journal of Derivatives*, 3(2).

---

*Repositorio bajo control de versiones Git. Para ver el historial de cambios: `git log --oneline`*
