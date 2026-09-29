# 📊 Executive Risk & Portfolio Dashboard - Créditos Hipotecarios

## 📌 Descripción del Proyecto
Desarrollo de un dashboard ejecutivo e interactivo en Power BI diseñado para el análisis de riesgo de crédito, el monitoreo del Índice de Morosidad (IMOR %) y la evaluación de Pérdida Esperada en un portafolio bancario de **$11.24 mil millones MXN** distribuido en 5,000 contratos activos.

Este proyecto combina el **análisis de datos tradicional (SQL, Python y DAX)** con el uso avanzado de **Inteligencia Artificial Generativa (Gemini)** como copiloto analítico, optimizando desde la generación de datos sintéticos con distribuciones reales de la banca mexicana hasta el diseño instruccional del dashboard ejecutivo.

![Dashboard Ejecutivo](dashboard_ejecutivo.png)

---

## 🤖 Co-creación y Uso de IA (Gemini + Data Analytics)
En línea con las demandas actuales del sector financiero e innovaciones en *Workplace AI*, este proyecto fue desarrollado integrando **Gemini** en cada etapa del ciclo de analítica:
* **Ingeniería de Prompting & Data Generation:** Asistencia en la lógica de scripts en Python para generar datasets de prueba con comportamientos probabilísticos del sistema financiero (ej. distribución de Score de Buró, matrices de atraso)[cite: 2].
* **Validación Estadística & Testing:** Ejecución de pruebas de hipótesis (ANOVA, correlación de Pearson) y scripts de Python para validar la consistencia lógica antes de construir la capa de semántica en Power BI[cite: 2].
* **Formulación DAX & UX Ejecutiva:** Refinamiento de fórmulas complejas en DAX y optimización de diseño visual (UI/UX) para comités directivos bancarios[cite: 6].

---

## 🛠️ Herramientas y Tecnologías
* **Power BI:** Modelado multidimensional de datos (Star Schema), diseño de la interfaz ejecutiva e interactividad.
* **DAX:** Lógica de negocio, métricas de riesgo crediticio, segmentación dinámica y tramos de mora.
* **Python (Pandas, NumPy, Matplotlib, SciPy):** Generación de bases sintéticas, limpieza de datos, análisis exploratorio (EDA) y validación de hipótesis de pérdida esperada.
* **SQL:** Estructuración y consulta de la base de datos relacional (`Fact_Creditos`, `Dim_Clientes`, `Dim_Sucursales`).
* **Gemini (Generative AI):** Co-piloto técnico para scripting, auditoría de código, cálculo de medidas y narrativa de negocio.

---

## 📐 Medidas DAX Principales Creadas en el Informe

A continuación se detallan las medidas clave utilizadas para alimentar los KPIs, las matrices de calor y los gráficos combinados[cite: 5]:

### 1. Saldo Total de Cartera
Calcula el saldo insoluto acumulado de la cartera activa[cite: 5].
`Saldo Total Cartera = SUM('Fact_Creditos_Hipotecarios'[Saldo_Insoluto])`

### 2. Cartera Vencida (> 90 días)
Suma el saldo insoluto de aquellos créditos con mora mayor a 90 días[cite: 4].
`Cartera Vencida = CALCULATE(SUM('Fact_Creditos_Hipotecarios'[Saldo_Insoluto]), 'Fact_Creditos_Hipotecarios'[Dias_Atraso] > 90)`

### 3. Índice de Morosidad (IMOR %)
Calcula la proporción de la cartera vencida respecto al saldo total de la cartera[cite: 5].
`IMOR % = DIVIDE([Cartera Vencida], [Saldo Total Cartera], 0)`

### 4. Pérdida Esperada Total (P.E.)
Métrica de provisión que evalúa el saldo acumulado en riesgo según los tramos de mora[cite: 4].
`Perdida Esperada Total = SUMX('Fact_Creditos_Hipotecarios', 'Fact_Creditos_Hipotecarios'[Saldo_Insoluto])`

### 5. Tasa de Incumplimiento (%)
Determina el porcentaje de contratos que cayeron en mora grave respecto al total de operaciones[cite: 6].
`Tasa_Incumplimiento_% = DIVIDE(CALCULATE(COUNTROWS('Fact_Creditos_Hipotecarios'), 'Fact_Creditos_Hipotecarios'[Dias_Atraso] > 90), COUNTROWS('Fact_Creditos_Hipotecarios'), 0)`

### 6. Columna Calculada: Banda de Score de Buró
Segmentación de clientes según su puntuación en Buró de Crédito[cite: 2].
`Banda_Score = SWITCH(TRUE(), 'Dim_Clientes'[Score_Buro] < 580, "1. Malo (<580)", 'Dim_Clientes'[Score_Buro] < 670, "2. Regular (580-669)", 'Dim_Clientes'[Score_Buro] < 750, "3. Bueno (670-749)", "4. Excelente (750+)")`

---

## 🐍 Código de Python: Generación de Datos Sintéticos

El siguiente script de Python fue utilizado para generar las bases de datos relacionales (`Dim_Clientes`, `Dim_Sucursales`, `Fact_Creditos_Hipotecarios`) garantizando coherencia en los comportamientos bancarios[cite: 2]:

```python
import pandas as pd
import numpy as np

# Configurar semilla de aleatoriedad
np.random.seed(42)
n_creditos = 5000

# 1. Generación de Dim_Clientes
id_clientes = [f"CLI-{i:05d}" for i in range(1, n_creditos + 1)]
tipos_cliente = np.random.choice(
    ['Persona Física', 'PF con Actividad Empresarial', 'Persona Moral'], 
    size=n_creditos, p=[0.74, 0.20, 0.06]
)
scores_buro = np.random.randint(580, 851, size=n_creditos)
ingresos = np.round(np.random.normal(65000, 20000, size=n_creditos), 2)

df_clientes = pd.DataFrame({
    'ID_Cliente': id_clientes,
    'Tipo_Cliente': tipos_cliente,
    'Score_Buro': scores_buro,
    'Ingreso_Mensual': np.maximum(ingresos, 15000)
})

# 2. Generación de Dim_Sucursales
sucursales_data = [
    ('SUC-01', 'Mérida Altabrisa', 'Yucatán', 'Mérida', 97130, 21.0163, -89.5878),
    ('SUC-02', 'Cancún Nichupté', 'Quintana Roo', 'Benito Juárez', 77500, 21.1456, -86.8324),
    ('SUC-03', 'Mérida Centro', 'Yucatán', 'Mérida', 97000, 20.9674, -89.6237),
    ('SUC-04', 'Mérida Montejo', 'Yucatán', 'Mérida', 97050, 20.9856, -89.6189),
    ('SUC-05', 'Mérida Poniente', 'Yucatán', 'Mérida', 97230, 20.9801, -89.6601),
    ('SUC-06', 'Playa del Carmen Centro', 'Quintana Roo', 'Solidaridad', 77710, 20.6274, -87.0799),
    ('SUC-07', 'Cancún Zona Hotelera', 'Quintana Roo', 'Benito Juárez', 77500, 21.1215, -86.7694),
    ('SUC-08', 'Campeche Malecón', 'Campeche', 'Campeche', 24000, 19.8456, -90.5369),
    ('SUC-09', 'Chetumal Centro', 'Quintana Roo', 'Othón P. Blanco', 77000, 18.5002, -88.2961),
    ('SUC-10', 'Ciudad del Carmen Principal', 'Campeche', 'Carmen', 24100, 18.6483, -91.8239)
]

df_sucursales = pd.DataFrame(sucursales_data, columns=[
    'ID_Sucursal', 'Nombre_Sucursal', 'Estado', 'Municipio', 'Codigo_Postal', 'Latitud', 'Longitud'
])

# 3. Generación de Fact_Creditos_Hipotecarios
id_creditos = [f"HIP-{i:05d}" for i in range(1, n_creditos + 1)]
id_sucursales_rand = np.random.choice(df_sucursales['ID_Sucursal'], size=n_creditos)
montos_originales = np.round(np.random.normal(3580000, 1650000, size=n_creditos), -3)
montos_originales = np.clip(montos_originales, 1000000, 12000000)

dias_atraso_prob = np.random.choice([0, 15, 45, 120, 150, 180], size=n_creditos, p=[0.783, 0.127, 0.066, 0.012, 0.007, 0.005])
saldos_insolutos = np.round(montos_originales * np.random.uniform(0.5, 0.95, size=n_creditos), 2)

df_creditos = pd.DataFrame({
    'ID_Credito': id_creditos,
    'ID_Cliente': id_clientes,
    'ID_Sucursal': id_sucursales_rand,
    'Fecha_Apertura': pd.date_range(start='2021-01-01', periods=n_creditos, freq='D').strftime('%Y-%m-%d'),
    'Monto_Original': montos_originales,
    'Saldo_Insoluto': saldos_insolutos,
    'Dias_Atraso': dias_atraso_prob
})

# Guardar archivos CSV
df_clientes.to_csv('Dim_Clientes.csv', index=False)
df_sucursales.to_csv('Dim_Sucursales.csv', index=False)
df_creditos.to_csv('Fact_Creditos_Hipotecarios.csv', index=False)
