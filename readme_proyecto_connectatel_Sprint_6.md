# 📱 Análisis de Comportamiento de Clientes — ConnectaTel

## 🎯 Objetivo del Proyecto
Evaluar el comportamiento real de los clientes de **ConnectaTel** (operaciones en México y Colombia) respecto al uso de servicios móviles (llamadas y mensajes de texto) con información registrada hasta el año **2024**. 

El propósito del análisis es:
* Identificar patrones de consumo según características demográficas y tipo de plan contratado.
* Detectar valores atípicos (*outliers*) y anomalías en los registros.
* Construir segmentaciones accionables de clientes (por edad y nivel de uso) para optimizar la oferta comercial, diseñar nuevos planes y reducir la tasa de abandono (*churn*).

---

## 📂 Datasets Utilizados
El análisis integra y cruza tres fuentes de datos relacionales:

1. **`plans.csv`** *(2 filas, 8 columnas)*:  
   Catálogo de planes comerciales actuales (`Basico` y `Premium`), detallando precio mensual, minutos incluidos, mensajes incluidos, GB incluidos y costos por consumo excedente.
2. **`users_latam.csv`** *(4,000 filas, 8 columnas)*:  
   Base demográfica y contractual de los clientes (`user_id`, nombre, apellido, edad, ciudad, fecha de registro, plan contratado y fecha de cancelación/churn).
3. **`usage.csv`** *(40,000 filas, 6 columnas)*:  
   Registro histórico transaccional del consumo real de cada usuario (`id`, `user_id`, tipo de interacción `call` o `text`, fecha, duración de llamadas en minutos y longitud de mensajes en caracteres).

---

## 🔄 Etapas del Análisis Realizadas
El flujo de trabajo en el Jupyter Notebook se divide en 7 etapas secuenciales:

1. **Carga y Exploración Inicial:** Importación de librerías (`pandas`, `numpy`, `matplotlib`, `seaborn`), lectura de los archivos CSV e inspección preliminar de dimensiones, columnas y tipos de datos.
2. **Diagnóstico de Calidad de Datos:** Identificación de valores faltantes, detección de valores centinela (`-999` en edad y `?` en ciudad) y auditoría de fechas fuera del horizonte temporal operativo (registros erróneos en el año 2026).
3. **Limpieza y Estandarización:**
   * Imputación de edades inválidas (`-999`) utilizando la mediana de edades válidas (48 años).
   * Estandarización del carácter `?` a valores nulos (`pd.NA`) en la columna `city`.
   * Conversión segura de fechas (`pd.to_datetime`) y marcado de fechas futuras (`> 2024`) como `NaT`.
   * Validación de nulos estructurales **MAR (*Missing At Random*)** en `duration` (55.19%) y `length` (44.74%), conservándolos intactos al depender de la naturaleza del evento (`call` vs `text`).
4. **Agregación y Perfil Estadístico (`user_profile`):** Agrupación de las transacciones por `user_id` para calcular métricas acumuladas (`cant_mensajes`, `cant_llamadas`, `cant_minutos_llamada`) y cruce con la tabla maestra de usuarios.
5. **Visualización y Detección de Outliers:** Análisis visual mediante histogramas segmentados por plan y detección estadística de valores extremos con *boxplots* y la regla del **Rango Intercuartílico (IQR)**, manteniendo a los usuarios extremos (*Power Users*) por su alto valor comercial.
6. **Segmentación de Clientes:**
   * **Por nivel de uso (`grupo_uso`):** Uso medio (73.58%), Bajo uso (19.48%) y Alto uso (6.95%).
   * **Por grupo etario (`grupo_edad`):** Adulto (50.45%), Adulto Mayor (30.55%) y Joven (19.00%).
7. **Insights Ejecutivos y Recomendaciones:** Síntesis estratégica para *stakeholders* con propuestas de creación de un plan intermedio, campañas de *cross-selling* hacia Premium y acciones de retención.

---

## 🚀 Cómo Ejecutar el Notebook

### Opción A: Ejecutar en Google Colab (Recomendado)
1. Abre el archivo `.ipynb` directamente en este repositorio de GitHub y haz clic en el botón **"Open in Colab"** (o descárgalo y súbelo a [Google Colab](https://colab.research.google.com/)).
2. En el panel izquierdo de Colab (ícono de carpeta 📁), sube los tres archivos CSV: `plans.csv`, `users_latam.csv` y `usage.csv`.
3. Asegúrate de que las rutas en `pd.read_csv()` apunten al directorio donde subiste los archivos (por ejemplo, `pd.read_csv('plans.csv')`).
4. En el menú superior, selecciona **Entorno de ejecución → Ejecutar todas** (*Runtime → Run all*).

### Opción B: Ejecutar de Forma Local (Jupyter Notebook / VS Code)
1. Clona este repositorio en tu computadora:
   ```bash
   git clone https://github.com/tu-usuario/connectatel-analysis.git
   cd connectatel-analysis
   ```
2. Instala las dependencias necesarias usando `pip` (se recomienda Python 3.9 o superior):
   ```bash
   pip install pandas numpy matplotlib seaborn jupyter
   ```
3. Inicia Jupyter Notebook desde la terminal:
   ```bash
   jupyter notebook
   ```
4. Abre el archivo `.ipynb` en tu navegador y ejecuta las celdas.

---

## 📋 Guía Rápida de Reproducción
Para reproducir los resultados y gráficos sin errores de ejecución:
* **Orden secuencial obligatorio:** Ejecuta siempre las celdas **de arriba hacia abajo sin saltar pasos**.
* **Dependencias internas:** El DataFrame unificado `user_profile` se construye en el **Paso 4**, y las columnas categóricas `grupo_uso` y `grupo_edad` se generan en el **Paso 6**. Si intentas ejecutar las celdas de visualización del Paso 5 o Paso 6.3 sin haber ejecutado previamente las celdas de creación de variables, Python arrojará un `KeyError` o `ValueError`.
* **Consistencia de datos:** No modifiques las reglas de imputación de la mediana en `age` ni elimines los *outliers* de consumo, ya que los porcentajes de segmentación del reporte ejecutivo dependen directamente de conservar la totalidad de los 4,000 usuarios activos.