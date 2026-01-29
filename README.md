# 📝 Análisis de Churn en Telecomunicaciones

## 💡 Introducción

El fenómeno de la "Churn" o evasión de clientes, donde los suscriptores deciden finalizar su relación con un proveedor de servicios, representa uno de los desafíos más críticos para las empresas de telecomunicaciones. La pérdida de clientes no solo impacta directamente en los ingresos y la cuota de mercado, sino que también implica costos significativos asociados con la adquisición de nuevos clientes para reemplazar a los perdidos.

El objetivo principal de este análisis es explorar los factores que contribuyen a la evasión de clientes en nuestra empresa de telecomunicaciones. A través de la extracción, transformación y carga de datos relevantes, buscaremos identificar patrones, comportamientos y características distintivas entre los clientes que permanecen y aquellos que se dan de baja. Al comprender mejor las causas subyacentes del churn, podremos desarrollar estrategias de retención más efectivas, personalizar ofertas y servicios, y, en última instancia, mejorar la lealtad de los clientes y la rentabilidad general de la empresa.

## 🧹 Limpieza y Tratamiento de Datos

Al inicio del análisis, se importaron los datos de la API en un DataFrame de Pandas, `df_telecom`, con 7267 filas y 6 columnas anidadas. Se realizaron las siguientes comprobaciones y limpiezas:

*   **Tipos de Datos y Valores Nulos**: La inspección inicial reveló que todas las columnas eran de tipo 'object' y no se encontraron valores nulos explícitos en las columnas principales. Las columnas anidadas se aplanaron exitosamente.
*   **Duplicados**: No se encontraron duplicados en la columna `customerID`, lo que asegura la unicidad de los clientes.
*   **Inconsistencias en 'Churn'**: Se identificaron 224 filas con un valor vacío en la columna `Churn`. Estas filas fueron eliminadas para asegurar la calidad del conjunto de datos para el análisis de evasión, resultando en un DataFrame final con 7043 entradas.
*   **Aplanamiento de Columnas Anidadas**: Las columnas anidadas (`customer`, `phone`, `internet`, `account`) se aplanaron exitosamente, expandiendo el DataFrame a 21 columnas y extrayendo detalles importantes de los servicios y cuentas de los clientes. La sub-columna `account_Charges` se aplanó en `account_Charges.Monthly` y `account_Charges.Total`.
*   **Manejo de 'account_Charges.Total'**: La columna `account_Charges.Total` se convirtió a tipo numérico y los valores nulos (que surgieron de la conversión) se rellenaron con la mediana (1391.0), asegurando que esta variable fuera utilizable para análisis cuantitativos. Esta corrección se realizó sin `inplace=True` para evitar advertencias.

## 📊 Análisis Exploratorio de Datos y Patrones Identificados

Después de la preparación de los datos, se obtuvieron las siguientes observaciones y se generaron visualizaciones clave:

### Resumen de la Inspección Inicial de Datos

*   **Tipos de Datos y Valores Nulos**: La inspección inicial reveló que todas las columnas eran de tipo 'object' y no se encontraron valores nulos explícitos en las columnas principales, aunque las columnas anidadas requerían un aplanamiento.
*   **Duplicados**: No se encontraron duplicados en la columna `customerID`, lo que asegura la unicidad de los clientes.
*   **Inconsistencias en 'Churn'**: Se identificaron 224 filas con un valor vacío en la columna `Churn`. Estas filas fueron eliminadas para asegurar la calidad del conjunto de datos para el análisis de evasión, resultando en un DataFrame final con 7043 entradas.
*   **Aplanamiento de Columnas Anidadas**: Las columnas anidadas (`customer`, `phone`, `internet`, `account`) se aplanaron exitosamente, expandiendo el DataFrame a 21 columnas y extrayendo detalles importantes de los servicios y cuentas de los clientes.
*   **Manejo de 'account_Charges.Total'**: La columna `account_Charges.Total` se convirtió a tipo numérico y los valores no numéricos se trataron como nulos y se rellenaron con la mediana, asegurando que esta variable fuera utilizable para análisis cuantitativos.

### Observaciones Clave de los Análisis Descriptivos y Univariados

*   **Distribución de 'Churn'**: El conjunto de datos muestra un desbalance significativo, con aproximadamente **73.5%** de clientes que **no** se dan de baja y **26.5%** que **sí** lo hacen. Esto indica que la mayoría de los clientes permanecen, pero un cuarto de ellos se va.
*   **Variables Categóricas**:
    *   **customer_gender**: La distribución de churn es bastante equilibrada entre hombres y mujeres, lo que sugiere que el género no es un factor determinante en la evasión.
    *   **account_Contract**: Los clientes con contratos de `Month-to-month` tienen una tasa de churn significativamente más alta en comparación con aquellos con contratos de `One year` o `Two year`. Los contratos a largo plazo parecen ser un fuerte indicador de lealtad.
    *   **account_PaymentMethod**: Los clientes que utilizan `Electronic check` como método de pago muestran una mayor propensión al churn en comparación con otros métodos como `Mailed check`, `Bank transfer (automatic)` o `Credit card (automatic)`.
*   **Variables Numéricas**:
    *   **customer_SeniorCitizen**: El 16.2% de los clientes son personas mayores, lo cual es un factor a considerar en el análisis.
    *   **customer_tenure**: La antigüedad promedio de los clientes es de aproximadamente 32 meses. Los clientes que se dan de baja tienden a tener una antigüedad mucho menor.
    *   **account_Charges.Monthly**: Los cargos mensuales varían ampliamente, con un promedio de 64.76 USD. Los clientes con cargos mensuales más altos son más propensos a la evasión.
    *   **account_Charges.Total**: Los cargos totales promedio son de 2281.91 USD, con una gran desviación estándar que refleja la diversidad en la duración del servicio y los cargos mensuales.

### Patrones Identificados en las Visualizaciones (KDE Plots)

Los gráficos de densidad (KDE plots) para las variables numéricas `customer_tenure`, `account_Charges.Monthly` y `account_Charges.Total` revelaron patrones distintivos en relación con el `Churn`:

1.  **`customer_tenure` (Antigüedad del cliente):**
    *   La distribución para los clientes que **no** se dan de baja (`No Churn`) muestra una clara tendencia hacia **mayores antigüedades**, con un pico significativo en clientes con más de 60 meses de servicio. Esto indica que los clientes leales tienden a permanecer por períodos prolongados.
    *   En contraste, la distribución para los clientes que **sí** se dan de baja (`Churn`) presenta un pico notable en las **antigüedades más bajas**, especialmente en el rango de 0 a 10 meses. Este hallazgo es crucial, ya que sugiere que una proporción considerable de la evasión ocurre en las primeras etapas del servicio.

2.  **`account_Charges.Monthly` (Cargos mensuales):**
    *   Para los clientes que **no** se dan de baja, la distribución es **más uniforme** a través de diferentes niveles de cargos mensuales, con una ligera concentración en cargos más bajos.
    *   Para los clientes que **sí** se dan de baja, se observa una clara concentración en **cargos mensuales más altos**, especialmente en el rango de los servicios de fibra óptica (aproximadamente entre 70 y 100 USD/mes). Esto indica que los clientes con facturas mensuales elevadas son más propensos a cancelar el servicio, posiblemente debido a una percepción de bajo valor o mejores ofertas de la competencia.

3.  **`account_Charges.Total` (Cargos totales):**
    *   La distribución de `account_Charges.Total` para `No Churn` presenta una **cola larga y se extiende a valores mucho más altos**, reflejando que los clientes con mayor antigüedad (y, por ende, mayores cargos totales acumulados) son más propensos a permanecer.
    *   Para los clientes con `Churn`, la distribución de `account_Charges.Total` está fuertemente **sesgada hacia valores bajos**, lo cual es consistente con el `customer_tenure` más bajo. Los clientes que se van pronto no acumulan grandes cargos totales.

## 📝 Conclusiones e Insights Clave

El análisis exploratorio de datos de TelecomX ha revelado varios patrones críticos que pueden ayudar a comprender y reducir la tasa de evasión de clientes (churn).

### Hallazgos Principales:

1.  **Antigüedad del Cliente (`customer_tenure`):**
    *   Existe una alta correlación entre la antigüedad del cliente y el churn. Los clientes con **poca antigüedad (0-10 meses)** son significativamente más propensos a darse de baja, lo que indica un problema en la fase de incorporación o en la satisfacción inicial con el servicio.
    *   Por el contrario, los clientes con **mayor antigüedad (más de 60 meses)** muestran una fuerte lealtad y una baja tasa de churn.

2.  **Cargos Mensuales (`account_Charges.Monthly`):**
    *   Los clientes que se dan de baja tienden a tener **cargos mensuales más elevados**, especialmente aquellos en el rango de 70-100 USD, lo cual a menudo se asocia con servicios de fibra óptica. Esto sugiere que el valor percibido no justifica el costo para estos clientes, o que encuentran ofertas más competitivas.
    *   Los clientes que permanecen muestran una distribución más variada, con una mayor proporción en rangos de cargos mensuales más bajos.

3.  **Cargos Totales (`account_Charges.Total`):**
    *   La distribución de los cargos totales para los clientes con churn está fuertemente sesgada hacia **valores bajos**, lo cual es coherente con su corta antigüedad. Esto refuerza la idea de que los clientes se van antes de acumular un gasto significativo.
    *   Los clientes que no se dan de baja acumulan cargos totales sustancialmente más altos, lo que refleja su larga relación con la empresa.

4.  **Tipo de Contrato (`account_Contract`):**
    *   Los clientes con contratos **mes a mes** presentan una probabilidad de churn mucho mayor en comparación con aquellos con contratos de uno o dos años. Los contratos a largo plazo actúan como un fuerte factor de retención.

5.  **Método de Pago (`account_PaymentMethod`):**
    *   Los clientes que utilizan **cheque electrónico** como método de pago tienen una tasa de churn notablemente más alta que otros métodos. Esto podría indicar una menor lealtad o una insatisfacción general asociada con este segmento de clientes.

## 🎯 Recomendaciones

Estos hallazgos proporcionan una base sólida para diseñar estrategias de retención proactivas:

*   **Intervención Temprana:** Dada la alta tasa de churn en los primeros meses, se deben implementar programas de onboarding mejorados, seguimientos proactivos y ofertas especiales para los clientes nuevos para asegurar su satisfacción inicial.
*   **Revisión de la Propuesta de Valor para Clientes de Alto Costo:** Es crucial analizar la oferta de servicios para clientes con altos cargos mensuales, especialmente de fibra óptica. Podrían beneficiarse de paquetes con valor agregado, descuentos por fidelidad o un mejor soporte al cliente para justificar el costo.
*   **Incentivos para Contratos a Largo Plazo:** Promover activamente la migración de clientes de contratos mes a mes a contratos de uno o dos años mediante descuentos o beneficios exclusivos puede reducir significativamente la rotación.
*   **Análisis del Método de Pago:** Investigar las razones detrás del alto churn entre usuarios de cheque electrónico. Podría haber patrones demográficos o de uso asociados que requieran atención específica o campañas de retención personalizadas.

Al centrarse en estos puntos clave, TelecomX puede desarrollar estrategias de retención más dirigidas y efectivas, mejorando la satisfacción del cliente y, en última instancia, reduciendo la pérdida de clientes.
