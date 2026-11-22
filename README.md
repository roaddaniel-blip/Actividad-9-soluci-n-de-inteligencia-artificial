## Rediseño integral de una solución de inteligencia artificial en la nube

Caso de estudio: Clasificador de sentimientos en español para BBVA. 

* Alumno: Daniel Roa Acosta 
* Escuela: Tecmilenio
* Máster en Inteligencia Artificial

__________________________________________________________________________________________________________________________

## Contenido 

Reporte Técnico
* **Análisis de la arquitectura actual:** Revisión de componentes, flujo de datos y cuellos de botella.
* **Propuesta de rediseño:** Estrategias para escalabilidad y optimización de costos.
* **Evaluación de métricas:** Análisis de desempeño y proyección financiera/costos.
* **Auditoría integral:** Revisión ética, técnica y de cumplimiento legal.
* **Plan de escalamiento responsable:** Hoja de ruta y gobernanza para el crecimiento del sistema.

Anexo Técnico
* **Tablas comparativas de costos:** Escenarios de infraestructura (actual vs. propuesto).
* **Métricas de desempeño:** Benchmarks de latencia, *throughput* y uso de recursos (CPU/RAM).
* **Evaluación de *fairness* y sesgos:** Análisis de equidad y mitigación de desviaciones en el modelo.

Diagrama de Arquitectura
* **Sistema actual:** Representación visual del despliegue base.
* **Sistema propuesto:** Representación visual de la arquitectura optimizada y escalable.



## Resumen ejecutivo

El modelo contempla el procesamiento de lenguaje natural pensado para detectar el sentimiento detrás de cada una de las encuentas aplicada a los clientes de BBVA con el objetivo de evaluar el servicio que recibieron en sucursal. 

A nivel técnico, toda esta lógica de inferencia se expone a través de una API construida en FastAPI, viene empaquetada dentro de un contenedor Docker y ya cuenta con una guía base para desplegarse en Google Cloud Run.

____________________________________________________________________________________________________________________________

## 1. Análisis de la arquitectura actual

### 1.1 Descripción técnica

El repositorio utilizado para este proyecto considera una solución de clasificación de sentimientos en español que combina procesamiento de lenguaje natural, servicios web y tokenización.

Los componentes principales de los que se compone son: 

| Componente | Tecnología | Función |
| :--- | :--- | :--- |
| **Modelo de IA** | RoBERTuito | Clasificación de sentimientos en español |
| **Backend** | Python y FastAPI | Exposición de servicios de inferencia |
| **Servidor** | Uvicorn | Procesamiento de solicitudes HTTP |
| **Contenedor** | Docker | Empaquetado de aplicación y dependencias |
| **Plataforma cloud propuesta** | Google Cloud Run | Ejecución administrada del contenedor |
| **Experimentación** | Jupyter Notebook y MLflow | Entrenamiento, comparación y seguimiento de experimentos |
| **Pruebas** | Pytest | Verificación funcional de la API |

### Flujo actual 

<img width="1338" height="784" alt="image_45da73aa" src="https://github.com/user-attachments/assets/72ad1b51-1100-4053-a52a-8b86f39d17fc" />

### Debilidades y fortalezas del modelo actual

| Categoría | Aspecto / Hallazgo | Impacto / Consecuencia potencial | Prioridad |
| :--- | :--- | :--- | :--- |
| **Fortaleza** | Separación funcional entre la API y el modelo | Facilita el mantenimiento y la evolución independiente de los componentes | N/A |
| **Fortaleza** | Uso de tecnologías estándar y validación básica | Mayor estabilidad, soporte comunitario y control inicial de entradas | N/A |
| **Fortaleza** | Contenerización y despliegue en Cloud Run | Alta portabilidad, menor carga operativa y escalabilidad automática | N/A |
| **Fortaleza** | Notebook de experimentación y uso de MLflow | Base sólida para garantizar la trazabilidad y reproducibilidad | N/A |
| **Debilidad** | Descarga de pesos del modelo durante la primera inferencia | Latencia elevada, dependencia de Internet y fallos de inicialización | **Alta** |
| **Debilidad** | Ausencia de métricas productivas verificadas | No es posible confirmar capacidad, SLA ni costo real | **Alta** |
| **Debilidad** | Pruebas unitarias con modelos simulados | No garantizan el comportamiento del modelo real | **Alta** |
| **Debilidad** | Configuración de escalabilidad no validada | Riesgo de saturación o sobreaprovisionamiento | **Alta** |
| **Debilidad** | Controles de acceso y privacidad no demostrados | Posible exposición de información enviada a la API | **Alta** |
| **Debilidad** | Auditoría de sesgos no documentada suficientemente | Riesgo de errores sistemáticos por grupo o variedad lingüística | **Alta** |

___________________________________________________________________________________________________________________________

## 2. Propuesta de rediseño para escalabilidad y optimización de costos.

### Escalabilidad

Para que el sistema funcione bien y sea fácil de mantener, es factible usar servicios en la nube donde el proveedor se encargue del mantenimiento básico, empaquetar el programa en piezas cerradas que no cambien por accidente y mantener separadas tareas clave:

* **El trabajo diario:** Responder a las consultas de los usuarios.
* **La vigilancia:** Monitorear que todo marche bien sin interrupciones.
* **Las reglas de control:** Supervisar cómo opera y evoluciona la inteligencia artificial.

Adicionalmente por temas de gobernanza y ética BBVA deberá ser responsable del resguardo de la información y garantizar que su tratamiento se haga bajo los lineamientos establecidos de ética y seguridad. 


| Rubro de Escalabilidad | Definición y Mecanismo | Configuraciones | Criterios  |
| :--- | :--- | :--- | :--- |
| **Escalabilidad Vertical**<br> | Aumentar la capacidad de cómputo (CPU y memoria RAM) dentro de una misma instancia. | • **Configuración A:** 1 vCPU / 2 GiB RAM *(Consumo mínimo)*<br>• **Configuración B:** 2 vCPU / 4 GiB RAM *(Equilibrio rendimiento/costo)*<br>• **Configuración C:** 4 vCPU / 8 GiB RAM *(Cargas de alta intensidad)* | Se elegirá la configuración menor que mantenga la RAM en límites seguros y cumpla la latencia objetivo bajo la concurrencia esperada. |
| **Escalabilidad Horizontal**<br> | Crear más instancias al subir la demanda y reducirlas cuando baja, usando señales de CPU y concurrencia en Cloud Run. | • **Concurrencia por instancia:** 1, 4, 8 y 16 solicitudes.<br>• **Instancias mínimas:** 0 en pruebas; evaluar 1 en producción si el *cold start* afecta el SLA.<br>• **Instancias máximas:** Límite inicial de 10.<br>• **Pruebas:** Carga progresiva y picos repentinos. |Debe ajustarse según la tasa de solicitudes, duración de inferencia, presupuesto y tolerancia a errores. |

| Configuración | Latencia p95 simulada | Throughput simulado | Memoria máxima | ¿Cumple meta / SLA? |
| :--- | :--- | :--- | :--- | :--- |
| **1 vCPU / 2 GiB** | 680 ms | 14.6 req/s | 79 % | No |
| **2 vCPU / 4 GiB** | 420 ms | 17.8 req/s | 68 % | Sí, en simulación |
| **4 vCPU / 8 GiB** | 295 ms | 19.7 req/s | 59 % | Sí, en simulación |

| Concurrencia configurada | Latencia p95 simulada | Throughput simulado | Interpretación |
| :--- | :--- | :--- | :--- |
| **1** | 260 ms | 3.5 req/s | Baja contención |
| **4** | 310 ms | 11.5 req/s | Mayor aprovechamiento |
| **8** | 420 ms | 17.8 req/s | Equilibrio preliminar |
| **16** | 760 ms | 20.5 req/s | Saturación y degradación de latencia |

### Optimización de costos.

Con el objetivo de garantizar una operación eficiente, escalable y sostenible de las soluciones de IA en BBVA, se propone una estrategia de optimización orientada a mejorar el rendimiento de los modelos, asegurar la continuidad operativa y gestionar eficientemente los recursos tecnológicos, manteniendo los niveles de servicio (SLA), los estándares de seguridad y los lineamientos de gobierno tecnológico del Banco.

a) Optimización
* Establecer un límite al largo de los textos para que no se sature.
* Comprimir el programa para que ocupe menos espacio y responda más rápido. 
* Se agruparan las respuestas por tipo de consulta de los usuarios y se dará respuesta dentro de los niveles de servicios establecidos. 

b) Estrategia FinOps:
* Etiquetar cada parte del sistema y activar alertas si el consumo sube más de lo esperado.
* Utilizar distintas combinaciones de potencia y formas de cobro para quedarnos con la más económica que mantenga buena velocidad.
* Limitar cuánto puede crecer el sistema automáticamente para evitar facturas sorpresa.
* Medir cuánto cuesta cada respuesta que da la inteligencia artificial y ajustar lo necesario.

__________________________________________________________________________________________________________________________

# Evaluación de métricas de desempeño y costo.

A fin de garantizar una arquitectura idónea para este proyecto es necesario establecer una línea base mediante pruebas controladas y compararla con las versiones optimizadas. De esta manera, se proponen las siguientes métricas de desempeño y costo: 
## Indicadores de desempeño 

| Indicador | Meta inicial propuesta | Método de medición |
| :--- | :--- | :--- |
| **Latencia p95** | ≤ 500 ms | Tiempo de respuesta de `/predict` con instancias calientes |
| **Latencia p99** | ≤ 1,000 ms | Percentil 99 de solicitudes |
| **Disponibilidad mensual** | ≥ 99.9 % | Solicitudes exitosas y sondas externas |
| **Tasa de errores del servicio** | < 1 % | Errores 5xx sobre solicitudes válidas |
| **Utilización de CPU** | 40–70 % en carga estable | Cloud Monitoring |
| **Memoria** | < 80 % del límite asignado | Métricas del contenedor |
| **Rendimiento** | Maximizar solicitudes/s cumpliendo p95 | Pruebas de carga |
| **Calidad predictiva** | Sin degradación significativa | Macro-F1 y métricas por clase |
| **Costo unitario** | Reducir respecto de la línea base | Facturación y volumen de inferencias |

## Metodología experimental: 

Las pruebas deben utilizar textos representativos, incluyendo entradas cortas y largas, lenguaje informal, errores ortográficos, emojis y expresiones regionales que concierne a todo BBVA. 

* E1. Línea base	Modelo original y configuración inicial	Determinar rendimiento real
* E2. Escalabilidad vertical	CPU y memoria	Identificar recursos óptimos
* E3. Escalabilidad horizontal	Concurrencia y número de instancias	Determinar capacidad bajo demanda
* E4. Optimización del modelo	Inferencia original frente a cuantización u ONNX	Medir velocidad, memoria y calidad
* E5. Resiliencia	Picos de tráfico, fallos y arranques en frío	Verificar recuperación y SLO

## Modelo de evaluación económica.

El costo de inferencia dependerá de la CPU y memoria, el tiempo de ejecución, número de solicitudes y modalidad de cobro. Los componentes adicionales incluirán los servicios efectivamente utilizados, como registro de imágenes, gestión de secretos y registros operativos: 

* Empleados BBVA: 48 mil a nivel nacional 
* Número de consultas mensuales estimadas: 20 consultas
* Total de consultas estimadas: 1,000,000 

| Variable | Demanda baja | Demanda media | Demanda alta |
| :--- | :--- | :--- | :--- |
| **Encuestas mensuales** | 10,000 | 50,000 | 100,000 |
| **Textos por encuesta** | 2 | 2 | 2 |
| **Procesamientos por texto** | 1 | 1 | 2 |
| **Inferencias mensuales** | 20,000 | 100,000 | 400,000 |
| **CPU por instancia** | 1 vCPU | 2 vCPU | 4 vCPU |
| **Memoria por instancia** | 2 GiB | 4 GiB | 8 GiB |
| **Instancias mínimas** | 0 | 0 | 1 |
| **Instancias máximas propuestas** | 3 | 5 | 10 |

| Componente | Baja | Media | Alta |
| :--- | :--- | :--- | :--- |
| **CPU de inferencia** | \$120 | \$720 | \$2,900 |
| **Memoria de inferencia** | \$60 | \$360 | \$1,450 |
| **Almacenamiento de imágenes** | \$20 | \$50 | \$100 |
| **Registros y monitoreo** | \$30 | \$100 | \$250 |
| **Red y otros servicios** | \$20 | \$70 | \$300 |
| **Total mensual** | **\$250** | **\$1,300** | **\$5,000** |


<img width="1114" height="944" alt="image_91d296d9" src="https://github.com/user-attachments/assets/2557218e-c725-43d9-8c51-5fe890767fcc" />

Sabremos que el rediseño funciona y está listo cuando logre equilibrar cuatro cosas al mismo tiempo: responder rápido, mantenerse siempre disponible, conservar su nivel de acierto y cuidar el costo por cada consulta.


______________________________________________________________________________________________________________________________________________

## Auditoría ética, técnica y legal.

La auditoría busca determinar si la solución puede procesar textos de forma confiable, segura, trazable y respetuosa de los derechos de las personas, agrupando todos los hallazgos, riesgos y controles en cuatro grandes vertientes:

| Rubro |Riesgos Identificados | Controles, Acciones y Criterios Clave |
| :--- | :--- | :--- |
| **1. Gobernanza**<br>*(Control, trazabilidad y operación)* | • **Falta de trazabilidad** *(Riesgo Alto)*<br>• **Degradación del modelo** *(Riesgo Medio-alto)*<br>• **Dependencia de terceros** *(Riesgo Medio-alto)*<br>• **Definición de roles y cambios** | • **Historial anónimo ("Ticket de compra"):** Registrar `request_id`, `timestamp`, `model_version`, `image_digest`, `latency_ms`, `status_code`, `prediction_label` y `policy_version` sin guardar el texto privado del usuario.<br>• **Mantenimiento continuo:** Monitoreo operativo, alertas de fallos y reevaluación periódica del modelo.<br>• **Control de cambios:** Artefactos verificados, dependencias fijadas, aprobación previa de actualizaciones y mecanismo de reversión.<br>• **Roles claros:** Asignar (y documentar) al dueño del proyecto, técnico de sistemas (MLOps), encargado de seguridad/privacidad y evaluador de IA. |
| **2. Ética**<br>*(Equidad, transparencia y uso adecuado)* | • **Sesgos lingüísticos** *(Riesgo Alto)*<br>• **Uso fuera del propósito** *(Riesgo Alto)*<br>• **Falta de explicabilidad** *(Un porcentaje alto no explica el porqué)* | • **Evaluación de sesgos:** Probar el modelo con textos reales de México, otras regiones, modismos, sarcasmo, negaciones y lenguaje inclusivo.<br>• **Regla de revisión humana:** Si la IA tiene dudas sobre una frase ambigua, debe pasarla a una **persona real** en lugar de adivinar.<br>• **Transparencia (Model Card):** Crear una ficha técnica sencilla con finalidad, origen de datos, métricas, limitaciones y casos prohibidos.<br>• **Alineación y límites:** Validar que las respuestas sigan los criterios y lineamientos de BBVA, avisar que es un análisis automatizado y usarlo solo como **apoyo**, nunca como juez definitivo. |
| **3. Cumplimiento**<br>*(Leyes, normativas y marcos internacionales)* | • **Protección de datos personales**<br>• **Clasificación de riesgo legal**<br>• **Propiedad intelectual y licencias**<br>• **Estándares internacionales** | • **Leyes locales y globales:** Cumplir con la legislación de protección de datos de **México** (y el RGPD si hay usuarios en Europa).<br>• **Ley de IA Europea (EU AI Act):** Evaluar obligaciones según el uso real (analizar reseñas tiene menor riesgo que evaluar personas).<br>• **Marcos de referencia:** Aplicar **NIST AI RMF** (gestión de riesgos), **ISO/IEC 42001** (gestión organizacional), **OECD/UNESCO** (principios éticos) e **IEEE 7000** (ética desde el diseño).<br>• **Licencias:** Verificar los permisos legales del modelo y los datos originales antes de distribuirlos o darles uso comercial. |
| **4. Seguridad**<br>*(Protección técnica y privacidad de datos)* | • **Tratamiento de datos personales** *(Riesgo Alto)*<br>• **Ataques contra la API** *(Riesgo Alto)*<br>• **Vulnerabilidades de software y accesos** | • **Privacidad desde el diseño:** Minimizar datos, no almacenar mensajes privados innecesariamente, anonimizar registros y fijar plazos de borrado.<br>• **Protección de API y red:** Autenticación, autorización, validación de entradas, límites de solicitudes (cuotas) y cifrado en tránsito (`HTTPS`).<br>• **Accesos e integridad:** Guardar contraseñas en *Secret Manager* con mínimos privilegios, actualizar contenedores y verificar que los pesos del modelo no hayan sido alterados. |

| Marco | Naturaleza | Aplicabilidad | Acción recomendada |
| :--- | :--- | :--- | :--- |
| **Legislación mexicana de protección de datos personales** | Obligatoria cuando corresponda | Tratamiento de datos personales bajo jurisdicción mexicana | Evaluar finalidad, base legal, avisos de privacidad, seguridad y derechos |
| **RGPD** | Reglamento obligatorio | Tratamientos comprendidos en su ámbito territorial y material | Evaluar bases jurídicas, derechos, transferencias y medidas de protección |
| **EU AI Act** | Reglamento obligatorio según ámbito y clasificación | Sistemas de IA sujetos a sus disposiciones | Determinar finalidad, rol del operador y clasificación de riesgo |
| **NIST AI RMF** | Marco voluntario | Gestión de riesgos de IA | Aplicar Govern, Map, Measure y Manage |
| **ISO/IEC 42001** | Estándar voluntario certificable | Sistema de gestión organizacional de IA | Documentar políticas, responsabilidades y mejora continua |
| **Principios OECD/UNESCO** | Directrices | Buenas prácticas de IA responsable | Integrar transparencia, equidad y responsabilidad |
_________________________________________________________________________________________________________________________________________________

## Plan de escalamiento responsable

A fin de garantizar una implementación exitosa se propone un plan de escalamiento que contempla 12 semanas: 

| Fase y Tiempo | Consideración | Entregable clave |
| :--- | :--- | :--- |
| **1. Diagnóstico y punto de partida**<br>*(Semanas 1–2)* | Revisar a fondo cómo está hecho el código hoy, probar el modelo real y medir cuánto tarda y cuánto cuesta actualmente. | Informe de estado inicial y lista de riesgos. |
| **2. Hacer el modelo más ligero y rápido**<br>*(Semanas 3–4)* | Ajustar cómo carga la IA para que use menos memoria y responda más rápido en CPU, cuidando que no pierda puntería en sus respuestas. | Modelo optimizado y comparativa de resultados. |
| **3. Preparar el crecimiento y cuidar el gasto**<br>*(Semanas 5–6)* | Configurar la nube para que soporte muchos usuarios al mismo tiempo, poner topes de presupuesto y hacer pruebas de estrés. | Arquitectura lista para probar y presupuesto validado. |
| **4. Revisión ética, legal y de seguridad**<br>*(Semanas 7–8)* | Revisar que la IA no tenga sesgos, proteger la privacidad de los datos, verificar licencias legales y definir responsables. | Matriz de riesgos, ficha técnica y plan de control. |
| **5. Lanzamiento gradual y seguro**<br>*(Semanas 9–10)* | Poner el sistema en marcha poco a poco, activar el monitoreo en vivo y tener listo un "botón de regreso" por si algo falla. | Despliegue piloto aprobado y funcionando. |
| **6. Cierre, validación y mejora continua**<br>*(Semanas 11–12)* | Confirmar que se cumplan las metas de velocidad, costo y calidad, probar las alertas de fallos y cerrar toda la documentación. | Reporte final de resultados y guía de seguimiento. |


_________________________________________________________________________________________________________________________________________________

## Conclusiones 

A lo largo de este reporte técnico hemos analizado y descubierto la manera en que la solución propuesta del análisis de sentimientos de las encuestas recibidas por BBAV que tiene como objetivo optimizar y establecer criterios para la mejor continua del servicio en la Red de sucursales de BBVA, propone un modelo más óptimo en donde se consideran los siguientes rubros: 

**Escalabilidad:** Cloud Run es una buena opción para ajustar la capacidad según lo que pidan los usuarios, pero no podemos adivinar los números; la cantidad exacta de CPU, memoria, concurrencia e instancias tiene que definirse haciendo pruebas reales.

**Optimización económica:** El verdadero ahorro está en dejar de pagar por capacidad que nadie usa, acortar el tiempo que tarda cada consulta y elegir bien el tamaño de los recursos. Eso sí, cualquier reducción de costos debe respaldarse con mediciones reales y tarifas oficiales, no con suposiciones.

**Calidad y desempeño:** Antes de moverle algo al sistema, necesitamos medir cómo se comporta el modelo real hoy en día. Solo así podremos asegurarnos de que hacerlo más rápido o ligero no arruine su puntería al clasificar textos.

**Gobernanza responsable:** No basta con tener buenas intenciones; hace falta dejar por escrito reglas claras para vigilar sesgos en el idioma, proteger la privacidad, asegurar la plataforma, rastrear cada respuesta, respetar las licencias y mantener siempre a una persona supervisando.

**Escalamiento sostenible:** Salir a producción tiene que hacerse paso a paso, poniendo filtros claros donde el sistema demuestre que funciona bien en lo técnico, es rentable en lo económico y cumple con la parte ética y legal antes de avanzar.


_________________________________________________________________________________________________________________________________________________

## Uso de declaratoria de IA 

| Herramienta utilizada | Etapa de la actividad | Prompt utilizado | Resultado obtenido | Validación o modificación realizada por el estudiante |
| :--- | :--- | :--- | :--- | :--- |
| **Gemini** | Consulta de información | Deberás seleccionar un proyecto de inteligencia artificial chatbot de servicio al cliente. El objetivo de este chatbot es que todos los colaboradores de BBVA puedan resolver sus dudas sobre procesos operativos, normativas institucionales e incidencias que afectan su operativa diaria. | Asistente inteligente conversacional para colaboradores de BBVA, diseñado como punto único de consulta para resolver dudas sobre procesos operativos, normativa institucional e incidencias que impactan la operación diaria. | Validación de contenido |
| **ChatGPT** | Creación de mapas MIRO | Crea un mapa MIRO con la información | Creación de imágenes | Validación |
| **ChatGPT** | Creación de imágenes | Creación de imágenes y mapas de flujo | Creación de imágenes y mapas de flujo | Validación |
| **ChatGPT** | Creación de PPT | Creación de PPT con uso de IA | Creación de PPT | Validación |
| **ChatGPT** | Revisión | Simulación de revisión y retroalimentación del proyecto. | Revisión | Revisión |



_________________________________________________________________________________________________________________________________________________

## Referencias

* Abu, M., Indra, N., Rahman, A., Sapiee, N., y Ahmad, I. (2019). *A study on Image Classification based on Deep Learning and Tensorflow*. International Journal of Engineering Research and Technology, 12(4), 563–569. Recuperado de [https://www.ripublication.com/irph/ijert19/ijertv12n4_16.pdf](https://www.ripublication.com/irph/ijert19/ijertv12n4_16.pdf)
* BBVA México. (2026). *El banco número uno* (recuperado el 31 de mayo de 2026). Recuperado de [https://www.bbva.mx/](https://www.bbva.mx/)
* Brownlee, J. (2019). *How to Classify Photos of Dogs and Cats (with 97% accuracy)*. Recuperado de [https://machinelearningmastery.com/how-to-develop-a-convolutional-neural-network-to-classify-photos-of-dogs-and-cats/](https://machinelearningmastery.com/how-to-develop-a-convolutional-neural-network-to-classify-photos-of-dogs-and-cats/)
* Cazarez. (2026). *Riesgos y beneficios en proyectos de IA* (recuperado el 13 de septiembre de 2026).
* Google. (2025). *Google Suite* (recuperado el 15 de junio de 2025). Recuperado de [https://www.google.com/search?q=historia+Google](https://www.google.com/search?q=historia+Google)
* Interactive Chaos. (2024). *El sistema nervioso* (recuperado el 15 de mayo de 2026). Recuperado de [https://interactivechaos.com/en/node/2268](https://interactivechaos.com/en/node/2268)
* Navarra. (2025). *Neurona biológica y artificial* (recuperado el 15 de mayo de 2026). Recuperado de [https://www.cun.es/diccionario-medico/terminos/neurona](https://www.cun.es/diccionario-medico/terminos/neurona)
* Pino Diez, R. (2022). *Introducción a la Inteligencia Artificial*. Madrid: Oviedo Publicaciones.
* Rouhiainen, L. (2017). *Inteligencia Artificial: 101 cosas que debes saber hoy sobre nuestro futuro*. Madrid: Alienta Editorial.
* SA. (2025). *Inteligencia artificial: definición, historia, usos, peligros* (recuperado el 15 de mayo de 2026). Recuperado de [https://datascientest.com/es/inteligencia-artificial-definicion](https://datascientest.com/es/inteligencia-artificial-definicion)
* Sossa Azuela, H. (2021). *Inteligencia Artificial aplicada*. México: Marcombo.
* TensorFlow. (s.f.). *Clasificación de imágenes*. Recuperado de [https://www.tensorflow.org/tutorials/images/classification](https://www.tensorflow.org/tutorials/images/classification)

