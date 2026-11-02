# Informe de Investigación: Análisis del Consumo Eléctrico en España (2016–2026)

**Fecha de informe:** Octubre 2026  
**Periodo analizado:** 1 de octubre de 2016 – 30 de septiembre de 2026  
**Fuente de Datos:** Red Eléctrica de España (REE) – API eSIOS / Demanda Peninsular en Tiempo Real  
**Herramientas de Visualización y ETL:** Python, Google Sheets, Data Studio (anteriormente Google Looker Studio)
**URL:** https://datastudio.google.com/reporting/fcb1c364-d3b9-4ec0-b9cd-318a1a373f21

---

## 1. Resumen 

El presente estudio analiza la demanda de energía eléctrica peninsular en España a lo largo de un periodo decenal (2016–2026). A partir del procesamiento de datos horarios oficial de Red Eléctrica de España (REE) y la categorización bajo el esquema tarifario de peajes de transporte y distribución **2.0TD**, se evalúan la estacionalidad, los comportamientos interanuales y la adaptación social a los tramos de consumo (**Valle, Llano y Punta**).

### Hallazgos Principales:
1. **Estabilidad Estructural:** A pesar de eventos macroeconómicos, fluctuaciones de precios y crisis sanitarias (como la pandemia de COVID-19 en 2020), el volumen de consumo nacional se ha mantenido dentro de un rango estructural altamente predecible.
2. **Ciclos Estacionales Invariables:** Se confirma un patrón bimodal de doble pico anual correspondiente a los meses de invierno (calefacción/climatización térmica en enero) y verano (refrigeración/aire acondicionado en julio-agosto), con un mínimo técnico recurrente en **abril**.
3. **Optimización por Tramo Valle:** La franja horaria **Valle** concentra cerca del **50% del consumo total acumulado**, lo que refleja una notable concienciación del consumidor (o una absorción natural por parte de consumos basales nocturnos e industriales).
4. **Resistencia en Tramos Caros (Punta vs. Llano):** A pesar de la señal de precio, el tramo **Punta** supera ligeramente al tramo **Llano** (diferencia de ~1,3%), demostrando rigidez en la demanda doméstica y laboral en las horas centrales del día (10:00-14:00 y 18:00-22:00).
5. **Comportamiento Semanal:** Los días centrales de la semana (**miércoles, jueves y martes**) registran la mayor intensidad de demanda, mientras que el **domingo** representa el mínimo semanal (~12% del total) debido al cese de actividad comercial e industrial masiva.

---

## 2. Metodología de Obtención y Tratamiento de Datos

### 2.1 Extracción (ETL Script en Python)
La ingesta de datos se realizó conectando mediante peticiones HTTP estructuradas en bloques de 14 días a la API oficial de Red Eléctrica (`apidatos.ree.es`), extrayendo la métrica de **demanda real peninsular expresada en MW** con resolución horaria.

```python
# Definición de periodos y consulta a REE
# Sistema de peajes 2.0TD aplicado:
# - Valle: Fines de semana, festivos nacionales y laborables (00:00 a 08:00)
# - Llano: Laborables (08:00-10:00, 14:00-18:00, 22:00-24:00)
# - Punta: Laborables (10:00-14:00, 18:00-22:00)
```

### 2.2 Enriquecimiento y Clasificación Tarifaria (Peaje 2.0TD)
Cada registro horario fue categorizado dinámicamente:
* **Festivos Nacionales Fijos:** Se catalogaron como *Valle* las 24 horas de días clave como 1 de enero, 6 de enero, 1 de mayo, 15 de agosto, 12 de octubre, 1 de noviembre, 6 de diciembre, 8 de diciembre y 25 de diciembre.
* **Fines de Semana:** Sábados y domingos integrados al 100% en tramo *Valle*.

### 2.3 Normalización y Limpieza en Looker Studio
* **Corrección de Escala:** Se normalizaron errores de magnitud.
* **Traducción y Estandarización Lingüística:** Con el fin de evitar discrepancias en la interfaz visual producidas por erratas en el *script* (ej. `mondasy`), se aplicó una regla de expresiones regulares en Looker Studio.

---

## 3. Análisis Desglosado por Páginas del Dashboard

```
========================================================================================
ESTRUCTURA DEL DASHBOARD (LOOKER STUDIO)
========================================================================================
├── PÁGINA 1: Evolución Temporal (Análisis interanual y estacional 2016-2026)
├── PÁGINA 2: Análisis por Tramos (Comportamiento tarifario y distribución semanal)
└── PÁGINA 3: Repositorio de Datos (Explorador de registros y consumo medio diario)
========================================================================================
```

---

### Página 1: Evolución Temporal (2016–2026)

Esta página evalúa el comportamiento macro de la demanda de electricidad en España durante la década analizada.

```
                  EVOLUCIÓN MENSUAL TÍPICA DEL CONSUMO (MW)
   
  Alto ──┐      ▲ (Enero: Calefacción)             ▲ (Julio/Agosto: A/A)
         │     / \                                / \
         │    /   \                              /   \
  Medio ─┼───/─────\────────────────────────────/─────\───
         │  /       \                          /       \
  Bajo  ─┴─/─────────\▲ (Abril: Mínimo)───────/─────────\───
           Ene  Feb  Mar  Abr  May  Jun  Jul  Ago  Sep  Oct  Nov  Dic
```

#### 1. Consumo Mensual por Meses (Estacionalidad):
* **Picos Térmicos:** Se aprecian dos máximos muy definidos. El más pronunciado históricamente corresponde a **enero** (uso intensivo de calefacción eléctrica y menor iluminación natural). El segundo pico ocurre entre **julio y agosto**, motivado por el uso masivo de aire acondicionado durante las olas de calor.
* **El Valle de Primavera:** El mes de **abril** registra sistemáticamente el mínimo de consumo anual. Esto se debe a temperaturas templadas que reducen la necesidad de climatización, combinado con festividades como la Semana Santa, donde cae la actividad industrial.

#### 2. Consumo Anual por Años:
* **Consideraciones Marginales (2016 y 2026):** 
  * **2016:** Al iniciar la muestra el 1 de octubre, refleja únicamente 3 meses de cómputo. Si se proyecta multiplicando por 4 (o triplicando su parcial respecto a 9 meses equivalentes), el volumen se sitúa exactamente en la media histórica.
  * **2026:** Al corte de datos en septiembre, faltan por computar los tres meses finales del año (octubre, noviembre y diciembre), justificación formal de su menor barra en el gráfico.
* **Máximos y Mínimos Históricos:** 
  * **2018** se consolida como el año de mayor volumen energético total del decenio.
  * **2023** registra el valle de consumo más pronunciado, favorecido por la eficiencia energética, autoconsumo fotovoltaico residencial en auge y temperaturas invernales moderadas.
  * **Tendencia Reciente (2023–2026):** Se observa una recuperación paulatina y ascendente en la demanda desde finales de 2023.

#### 3. Serie Histórica Completa (Evolución 2016–2026):
* **Cambio de Patrón Energético (Post-2022):** Hasta 2021, el pico invernal de enero superaba con claridad al pico estival de verano. Sin embargo, a partir de 2022 la tendencia se invierte o equipara: **los picos de verano (julio/agosto) pasan a ser más agresivos que los de invierno**, un síntoma claro del cambio climático y la mayor electrificación del confort térmico en verano.
* **Causas del Incremento Base:** La elevación de la demanda base residencial/tecnológica responde al incremento de equipamiento electrificado en los hogares (centros de cómputo domésticos, impresoras 3D, electrodomésticos conectados, carga de vehículos eléctricos y climatización constante).

---

### Página 2: Análisis por Tramos Horarios y Días

Esta sección desglosa la demanda interna atendiendo a la estructura tarifaria y al calendario laboral.

```
       DISTRIBUCIÓN PORCENTUAL                    REPARTO POR DÍA DE LA SEMANA
         POR TRAMO HORARIO                         (PORCENTAJE DE CONSUMO)
         
         ┌───────────────┐                          Mie ─── [14.9%]
         │  Valle ~50%   │                          Jue ─── [14.8%]
         │  (Económico)  │                          Mar ─── [14.7%]
         ├───────┬───────┤                          Vie ─── [14.5%]
         │ Punta │ Llano │                          Lun ─── [14.4%]
         │ 25.6% │ 24.3% │                          Sab ─── [14.2%]
         └───────┴───────┘                          Dom ─── [12.5%]
```

#### 1. Distribución Porcentual y Volumen de Tramos (Punta, Llano, Valle):
* **Tramo Valle (~50%):** Es la franja dominante. Representa casi la mitad de la energía consumida en España. Esto confirma una respuesta eficiente por parte de la ciudadanía y la industria, desplazando consumos programables a horas nocturnas y fines de semana.
* **Tramo Punta vs. Llano:** 
  * **Punta:** Ocupa el segundo lugar en volumen. Al ser la franja con el precio del kWh más elevado, refleja una inelasticidad importante: las horas de mayor actividad comercial/laboral (10-14h y 18-22h) coinciden de forma inevitable con las rutinas de la población.
  * **Llano:** Se sitúa solo un **1.3% por debajo del tramo Punta**, sirviendo de puente de transición tarifaria.

#### 2. Reparto del Consumo por Días de la Semana:
* **Homogeneidad Laboral (14% - 15%):** La demanda de lunes a viernes se mantiene extremadamente estable, fluctuando en un rango muy estrecho.
* **Ranking Semanal:** **Miércoles** encabeza ligeramente el volumen total, seguido en orden por **jueves, martes, viernes y lunes**.
* **El Efecto Fin de Semana:** El **sábado** registra un leve descenso, mientras que el **domingo** marca el mínimo semanal absoluto (**~12%**). Esta brecha de más de 2.5 puntos respecto a los días centrales responde al parón de la industria pesada y la actividad corporativa/institucional.

#### 3. Evolución Mensual por Tramos Horarios:
* La gráfica combinada muestra que el consumo en **tramo Valle** se intensifica sustancialmente en los meses extremales (enero y julio/agosto). Los usuarios programan electrodomésticos y climatizadores en horas nocturnas para mitigar el impacto en la factura eléctrica durante los meses climáticamente más exigentes.

---

### Página 3: Repositorio de Datos y Consumo Medio Diario

La última página combina la capacidad de auditar datos brutos con tarjetas de síntesis y ordenación ejecutiva.

```
========================================================================================
CABECERA DE PÁGINA: [ Filtro Dinámico de Año: 2016-2026 ]
----------------------------------------------------------------------------------------
KPI 1: Consumo Total Selección   | KPI 2: Promedio Diario      | KPI 3: Pico Máximo
========================================================================================
```

#### 1. Consumo Medio Diario por Tramos Horarios (Matriz de Ordenación):
* Al ordenar los días de la semana y los tres tramos horarios según la intensidad de **consumo Valle**, la estructura jerárquica muestra ligeros matices:
  * **Orden de Intensidad:** Miércoles, Jueves, Martes, Viernes, Lunes, Sábado, Domingo.
* **Dominancia de Hora Punta en Días Laborables:** Al comparar internamente las columnas de laborables, la franja **Punta** supera de forma sistemática a la franja **Llano**. En fines de semana (sábado y domingo), toda la demanda informática y registral computa automáticamente en **Valle** según normativa.

#### 2. Explorador de Registros (Tabla Detallada):
* Permite la auditoría puntual a nivel de fecha/hora (`fecha_hora`, `hora`, `dia_semana`, `consumo_mw`, `periodo_tarifario`). Sirve como repositorio transparente para verificación metodológica.

---

## 4. Identidad Visual y Paleta de Colores en Looker Studio

Para garantizar la autoridad científica, legibilidad institucional y sobriedad del informe en Looker Studio, se adoptó la siguiente guía de estilo visual:

### 4.1 Paleta Cromática Aplicada
* **Fondo y Contenedores:** Colores fríos y limpios con dominancia de **blancos purificados** (`#FFFFFF`) para evitar fatiga visual en la lectura de números.
* **Texto y Estructura Principal:** **Azul Marino / Pizarra Oscuro** (`#1E293B`), garantizando contraste perfecto y seriedad institucional.
* **Simbología de Tramos Tarifarios (Convención Visual):**
  * 🟢 **Tramo Valle:** **Verde Esmeralda** (`#10B981`): Simboliza ahorro, menor coste y eficiencia.
  * 🟡 **Tramo Llano:** **Amarillo / Ámbar** (`#F59E0B`): Simboliza neutralidad / precaución.
  * 🔴 **Tramo Punta:** **Rojo / Naranja Oscuro** (`#EF4444`): Simboliza máxima alerta de coste e intensidad.

### 4.2 Tipografía
* **Familia Principal:** `Roboto`
* **Escala de Tamaños:**
  * Títulos de Página: **30 px (Bold)**
  * Títulos de Gráficos: **18 px (Regular)**
  * Valores de Tablas y Ejes: **12 px (Regular)**

---

## 5. Conclusiones y Recomendaciones de Investigación

1. **Inelasticidad del Consumo:** La demanda eléctrica en España ha demostrado ser notablemente inelástica a nivel global. Pese a fluctuaciones económicas, el volumen global consumido responde a patrones climáticos e industriales consolidados.
2. **Éxito en la Discriminación Horaria Nocturna:** El elevado porcentaje del **tramo Valle (~50%)** demuestra que la estructura del peaje 2.0TD introducida por la CNMC y el Ministerio para la Transición Ecológica ha sido integrada eficientemente por el sistema eléctrico peninsular.
3. **Punto de Atención (Días Centrales):** Los **martes, miércoles y jueves** registran la mayor concentración de carga y tensión en horas Punta. Se recomienda a gestores energéticos promover incentivos adicionales en estas franjas para aplanar la curva de carga nacional.
