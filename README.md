# 📞 ConnectaTel: Análisis de Datos, Limpieza y Segmentación de Clientes

Este repositorio contiene el desarrollo del proyecto de analítica avanzada para la empresa de telecomunicaciones **ConnectaTel**. El objetivo principal es consolidar fuentes de datos fragmentadas, diagnosticar y mitigar problemas de calidad de la información, aislar comportamientos atípicos (*outliers*) y construir una segmentación estratégica de clientes para optimizar la toma de decisiones comerciales.

---

## 📑 Estructura del Proyecto

El análisis se divide de manera rigurosa en las siguientes etapas clave:
1. **Consolidación de Datos:** Unión mediante `left merge` del catálogo de usuarios (`users`) con las métricas agregadas de consumo (`usage`).
2. **Control de Calidad y Diagnóstico:** Identificación y corrección de anomalías, valores centinela y registros corruptos.
3. **Análisis de Outliers (Valores Atípicos):** Implementación del método de Rango Intercuartílico (IQR) para definir umbrales de corte operativos.
4. **Segmentación de Clientes:** Clasificación demográfica (Edad) y de comportamiento operativo (Nivel de Uso).
5. **Generación de Insights y Recomendaciones:** Acciones estratégicas orientadas al negocio.

---

## ⚠️ Problemas de Calidad Detectados y Soluciones

Durante la fase de auditoría de datos, se intervinieron las siguientes variables críticas:

* **Edad (`age`):** Se aisló el uso de un valor centinela (`-999`) que actuaba como marcador de error en menos del **1%** del dataset. Fue corregido mediante imputación limpia utilizando la **mediana (47 años)**.
* **Ciudad (`city`):** Un total de **469 registros (11.73%)** contenían el caracter especial `"?"`. Se estandarizaron a valores nulos (`np.nan` / "unknown") para no alterar las métricas de penetración geográfica sin perder el historial de tráfico de esos clientes.
* **Fecha de Registro (`reg_date`):** Se identificaron inconsistencias con registros datados en el año **2026** (fuera del límite lógico del negocio). Se forzaron a tipo nulo (`np.nan`).
* **Ausencias Estructurales en Uso:** Las columnas `duration` (llamadas) y `length` (mensajes) presentaban **55.19%** y **44.74%** de valores faltantes. Se validó estadísticamente que responden al mecanismo **Missing at Random (MAR)**, ya que si el evento es un mensaje de texto, la duración de llamada es nula por naturaleza (y viceversa). Se conservaron intactos en el modelo.

---

## 📊 Segmentación Estratégica

### 1. Perfiles por Edad (Demográfico)
La base de clientes de ConnectaTel muestra una distribución notablemente **uniforme**, dividiéndose en tres grandes segmentos:
* **Jóvenes (< 30 años):** Segmento emergente (~750 usuarios).
* **Adultos (30-60 años):** El **núcleo del negocio** (~2,000 usuarios), proporcionando la mayor estabilidad de ingresos y volumen de tráfico predecible.
* **Adultos Mayores (> 60 años):** Grupo de alto volumen (~1,200 usuarios) con patrones de consumo muy estables.

### 2. Perfiles por Nivel de Uso (Comportamiento)
Clasificados dinámicamente mediante lógica condicional cruzada secuencial (`np.select`):
* **Bajo Uso:** Clientes con < 5 llamadas y < 5 mensajes (~800 usuarios). Uso reactivo o de emergencia.
* **Uso Medio:** El **motor comercial** de la compañía (entre 5 y 10 llamadas/mensajes, ~3,000 usuarios). Muestran un comportamiento orgánico óptimo.
* **Alto Uso:** Consumidores intensivos que superan los umbrales estándar de la red (~300 usuarios).

---

## 🔍 Análisis de Patrones Extremos (Outliers)

Utilizando diagramas de caja (*Boxplots*) combinados con el método matemático IQR, se analizaron los límites superiores:
* **Mensajes y Llamadas:** El IQR sugirió un límite estricto de **7.5**. Sin embargo, recortar en este umbral significaría alterar el **19.1%** de los datos de mensajes (765 filas) y el **8.8%** de llamadas (353 filas). Como los máximos reales son de 17 mensajes y 15 llamadas (comportamiento humano legítimo), **se decidió mantenerlos intactos** para conservar la variabilidad real.
* **Minutos de Llamada:** El límite IQR se fijó en **50.74 minutos**. Se detectaron **222 usuarios (5.5%)** con consumos extremos de hasta **155.69 minutos**. Estos casos representan cuentas corporativas, fraude o líneas abiertas accidentales que distorsionan el costo de interconexión de red.

---

## 💡 Recomendaciones de Negocio

1.  **Optimización del Plan Básico (Capping Analítico):** Aplicar un truncamiento (*capping*) en **50.74 minutos** para los análisis del plan base, protegiendo las métricas de dispersión de la compañía.
2.  **Lanzamiento de "Connecta Pro / Ejecutivo":** Crear un plan premium de alta gama (tarifa plana de USD 35 a USD 40) para capturar de manera rentable el valor marginal del 5.5% de usuarios extremos que consumen más de 50 minutos por llamada.
3.  **Plan Fidelidad "Conectados" (Adulto Mayor):** Aprovechar el volumen de más de 1,200 usuarios mayores de 60 años para ofrecer un plan básico con tarifa preferencial a cambio de contratos de permanencia a largo plazo, asegurando un flujo de caja predecible con bajo impacto de saturación en la red.

---

## 🛠️ Tecnologías Utilizadas

* **Python 3**
* **Pandas:** Manipulación, fusión (`merge`) y limpieza de estructuras de datos.
* **NumPy:** Lógica condicional indexada mediante `np.select()` e imputaciones de nulos.
* **Matplotlib & Seaborn:** Visualización avanzada de datos (`boxplot` secuenciales en bucles y gráficos de conteo categórico `countplot`).

---
*Proyecto desarrollado como parte del proceso de optimización analítica de datos e inteligencia de negocios corporativa.*
