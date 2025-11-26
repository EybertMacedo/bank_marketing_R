# DATACARD - Bank Marketing Dataset Analysis

## Contexto del Proyecto

El objetivo principal de este proyecto es encontrar las mejores estrategias para mejorar la próxima campaña de marketing de una institución financiera. Para lograr esto, se analiza la última campaña realizada por el banco para identificar patrones que permitan desarrollar estrategias futuras más efectivas.

El problema se aborda como una tarea de **clasificación**, donde la variable objetivo es `deposit` (si el cliente realizó un depósito a plazo: `yes`/`no`).

### Fuente de Datos

> [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, June 2014

##  Tecnologías y Librerías

El análisis fue realizado utilizando **R**. Las siguientes librerías son necesarias para replicar el estudio:

```r
install.packages(c("dplyr", "ggplot2", "readr", "caret", "MASS", "rpart", "class", "kernlab", "randomForest", "e1071", "pROC", "caTools"))
```

* **Manipulación de datos:** `dplyr`, `readr`
* **Visualización:** `ggplot2`
* **Machine Learning:** `caret`, `MASS`, `rpart`, `class`, `kernlab`, `randomForest`, `e1071`
* **Métricas:** `pROC`

## Descripción del Dataset

El dataset contiene **11,162 registros** y las siguientes variables clave:

| Variable | Descripción | Tipo |
| :--- | :--- | :--- |
| **age** | Edad del cliente. | Numérico |
| **job** | Tipo de trabajo (admin, technician, etc.). | Categórico |
| **marital** | Estado civil. | Categórico |
| **education** | Nivel educativo. | Categórico |
| **default** | ¿Tiene crédito en incumplimiento? | Binario |
| **balance** | Saldo promedio anual (EUR). | Numérico |
| **housing** | ¿Tiene préstamo de vivienda? | Binario |
| **loan** | ¿Tiene préstamo personal? | Binario |
| **contact** | Tipo de comunicación usada. | Categórico |
| **day/month** | Día y mes del último contacto. | Temporal |
| **duration** | Duración del último contacto (segundos). | Numérico |
| **campaign** | Número de contactos en esta campaña. | Numérico |
| **pdays** | Días desde el contacto anterior (-1 si no hubo). | Numérico |
| **previous** | Número de contactos previos. | Numérico |
| **poutcome** | Resultado de la campaña anterior. | Categórico |
| **deposit** | **Variable Objetivo** (Suscripción al depósito). | Binario |

## Análisis Exploratorio de Datos (EDA)

Se realizaron visualizaciones extensivas para entender el comportamiento de los clientes.

### Hallazgos Clave de la Segmentación

1. **Edad y Educación:**
   * Los clientes más jóvenes suelen tener educación superior.
   * Los mayores de 65 años (jubilados) tienen los saldos más altos.

2. **Estado Financiero:**
   * La mayoría de los clientes no tiene deudas pendientes ni préstamos personales.
   * **Housing:** Los clientes entre 45-55 años son los más propensos a tener hipotecas. Los jubilados raramente las tienen.

3. **Variable `Duration`:**
   * Es el predictor más fuerte. Una mayor duración de la llamada está altamente correlacionada con el éxito (depósito), aunque esta variable no se conoce antes de realizar la llamada.

### Tratamiento de Datos (Cleaning & Feature Engineering)

* **Outliers:** Se detectaron outliers en `balance` y `duration`. Se aplicó una técnica de **Clamping (Winsorization)** para limitar los valores extremos utilizando el Rango Intercuartílico (IQR).
* **Feature Engineering:** Se creó la variable compuesta `marital_edu` para segmentar mejor los grupos.
* **Codificación:**
  * Label Encoding para variables binarias.
  * One-Hot Encoding para variables categóricas multiclase.
  * Manejo de valores raros (<5%) agrupándolos en la categoría "other".
* **Escalado:** Se utilizó escalado Min-Max para normalizar variables numéricas.

## Modelado Predictivo

Se probaron múltiples algoritmos de clasificación dividiendo los datos en **70% Entrenamiento** y **30% Prueba**.

### Modelos Evaluados

1. **LDA** (Linear Discriminant Analysis)
2. **QDA** (Quadratic Discriminant Analysis) - Usando componentes principales (PCA).
3. **CART** (Classification and Regression Trees)
4. **KNN** (K-Nearest Neighbors)
5. **SVM** (Support Vector Machines) - Kernel Radial.
6. **Random Forest**

### Resultados

El modelo **Random Forest** demostró ser uno de los más robustos, destacando las siguientes variables como las más importantes:

1. **Duration** (Crítica, aunque operativa).
2. **Balance**
3. **Month**
4. **Age**

## Conclusiones y Estrategia

Para futuras campañas, la institución financiera debería:

1. **Focalizar en demografías clave:** Los **estudiantes** y **jubilados** mostraron saldos más altos y tasas de interés interesantes.
2. **Personalizar por Historial:** Los clientes que tuvieron éxito en campañas anteriores (`poutcome = success`) tienen alta probabilidad de conversión.
3. **Gestión de Llamadas:** Dado que la `duration` es vital, las estrategias de guion deben enfocarse en mantener el interés del cliente en los primeros segundos para extender la interacción.
4. **Segmentación por Educación/Estado Civil:** Los solteros con educación superior tienen menos carga de préstamos y mayor saldo, siendo un target ideal para productos de inversión.