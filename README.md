## Rediseño integral de una solución de inteligencia artificial en la nube

Caso de estudio: Clasificador de sentimientos en español para BBVA. 

* Alumno: Daniel Roa Acosta 
* Escuela: Tecmilenio
* Máster en Inteligencia Artificial

__________________________________________________________________________________________________________________________

## Contenido 

## Reporte Técnico
* **Análisis de la arquitectura actual:** Revisión de componentes, flujo de datos y cuellos de botella.
* **Propuesta de rediseño:** Estrategias para escalabilidad y optimización de costos.
* **Evaluación de métricas:** Análisis de desempeño y proyección financiera/costos.
* **Auditoría integral:** Revisión ética, técnica y de cumplimiento legal.
* **Plan de escalamiento responsable:** Hoja de ruta y gobernanza para el crecimiento del sistema.

## Anexo Técnico
* **Tablas comparativas de costos:** Escenarios de infraestructura (actual vs. propuesto).
* **Métricas de desempeño:** Benchmarks de latencia, *throughput* y uso de recursos (CPU/RAM).
* **Evaluación de *fairness* y sesgos:** Análisis de equidad y mitigación de desviaciones en el modelo.

## Diagrama de Arquitectura
* **Sistema actual:** Representación visual del despliegue base.
* **Sistema propuesto:** Representación visual de la arquitectura optimizada y escalable.



## Resumen ejecutivo

El modelo contempla el procesamiento de lenguaje natural pensado para detectar el sentimiento detrás de cada una de las encuentas aplicada a los clientes de BBVA con el objetivo de evaluar el servicio que recibieron en sucursal. 

A nivel técnico, toda esta lógica de inferencia se expone a través de una API construida en FastAPI, viene empaquetada dentro de un contenedor Docker y ya cuenta con una guía base para desplegarse en Google Cloud Run.

____________________________________________________________________________________________________________________________

## 1. Análisis de la arquitectura actual

### 1.1 Descripción técnica

El repositorio `Fase-II-Gestión-de-proyectos-IA` documenta una solución de clasificación de sentimientos en español que combina procesamiento de lenguaje natural, servicios web y contenerización.

Los componentes principales identificados son:

| Componente | Tecnología | Función |
| :--- | :--- | :--- |
| **Modelo de IA** | RoBERTuito | Clasificación de sentimientos en español |
| **Backend** | Python y FastAPI | Exposición de servicios de inferencia |
| **Servidor** | Uvicorn | Procesamiento de solicitudes HTTP |
| **Contenedor** | Docker | Empaquetado de aplicación y dependencias |
| **Plataforma cloud propuesta** | Google Cloud Run | Ejecución administrada del contenedor |
| **Experimentación** | Jupyter Notebook y MLflow | Entrenamiento, comparación y seguimiento de experimentos |
| **Pruebas** | Pytest | Verificación funcional de la API |
