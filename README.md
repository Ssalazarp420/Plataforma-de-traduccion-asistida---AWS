# 🌐 Plataforma de Traducción Asistida (CAT Tool) Serverless

**Proyecto AWS #27 - Implementación Serverless Event-Driven**

## 📋 Descripción
[cite_start]Este proyecto es una herramienta de traducción asistida por computadora (CAT Tool) construida sobre una arquitectura 100% Serverless en AWS[cite: 1082, 1086]. [cite_start]El sistema permite a los usuarios subir documentos de texto, los cuales son procesados automáticamente para segmentarlos en oraciones y traducirlos (mediante simulación o integración con IA) para su posterior revisión en una interfaz web[cite: 1094].

[cite_start]El objetivo principal fue implementar un flujo de trabajo reactivo (Event-Driven) que escala automáticamente sin necesidad de administrar servidores[cite: 1321].

## 🏗️ Arquitectura
El flujo de datos sigue un modelo reactivo iniciado por la carga de archivos:

![Diagrama de Arquitectura](readme-images/arquitectura.png)
*(Nota: Sube la captura de la página 4 de tu PDF a la carpeta readme-images)*

### Flujo de Trabajo:
1.  [cite_start]**Ingesta:** El usuario sube un archivo `.txt` al bucket de **S3** en la carpeta `uploads/`[cite: 1104].
2.  [cite_start]**Procesamiento:** El evento `ObjectCreated` dispara la función **AWS Lambda** (`cat-process-document`)[cite: 1226].
3.  [cite_start]**Lógica de Negocio:** La función segmenta el texto y, debido a restricciones de laboratorio, aplica una **Lógica Híbrida de Traducción** (simulación en caso de fallo de permisos IAM)[cite: 1227, 1245].
4.  [cite_start]**Persistencia:** Los segmentos procesados se guardan en **Amazon DynamoDB** para mantener el estado y permitir la edición granular[cite: 1098, 1168].
5.  [cite_start]**Visualización:** El Frontend consulta los datos a través de **API Gateway**, que invoca a la Lambda `cat-api-handler`[cite: 1256].

### Stack Tecnológico:
* [cite_start]**Compute:** AWS Lambda (Python 3.9)[cite: 1226].
* [cite_start]**Storage:** Amazon S3 (Buckets con estructura `uploads/` y `processed/`)[cite: 1103].
* [cite_start]**Database:** Amazon DynamoDB (Tablas: `TranslationProjects`, `TranslationSegments` en modo On-Demand)[cite: 1160].
* [cite_start]**API:** Amazon API Gateway (HTTP API)[cite: 1256].
* [cite_start]**Frontend:** SPA con HTML5, Bootstrap y JavaScript Vanilla[cite: 1279].

## ⚙️ Configuración y Desafíos Técnicos

### Modelo de Datos (DynamoDB)
[cite_start]Se diseñaron dos tablas principales para optimizar el acceso[cite: 1160]:
* [cite_start]**TranslationProjects:** `PK: project_id` (Gestiona el estado global del archivo)[cite: 1161, 1164].
* [cite_start]**TranslationSegments:** `PK: project_id`, `SK: sent_id` (Almacena cada oración individualmente)[cite: 1165, 1166].

### Soluciones a Problemas Encontrados (Troubleshooting Log)
[cite_start]Durante el desarrollo en el entorno *AWS Learner Lab*, se superaron los siguientes obstáculos técnicos[cite: 1315]:

| Error / Síntoma | Causa Raíz | Solución Implementada |
| :--- | :--- | :--- |
| **Decimal is not JSON serializable** | [cite_start]DynamoDB devuelve números como objetos `Decimal`, incompatibles con el JSON nativo de Python[cite: 1258, 1260]. | [cite_start]Se implementó una clase personalizada `DecimalEncoder` en la Lambda del API[cite: 1261, 1268]. |
| **CORS / Error Rojo en consola** | [cite_start]Bloqueo de seguridad del navegador al abrir archivos locales (`origin: null`)[cite: 1282, 1283]. | [cite_start]Se levantó un servidor local con `python -m http.server 8000` y se habilitó CORS en API Gateway [cite: 1310-1312]. |
| **AccessDenied en Translate** | [cite_start]Restricciones del rol `LabRole` en el entorno educativo[cite: 1318]. | [cite_start]Implementación de bloque `try/except` con *fallback* a traducción simulada (`[ES Simulado]`)[cite: 1245, 1318]. |

## 🚀 Instalación y Uso Local

1.  **Clonar el repositorio:**
    ```bash
    git clone [[https://github.com/tu-usuario/aws-cat-tool.git](https://github.com/Ssalazarp420/Plataforma-de-traduccion-asistida---AWS/tree/main)]([https://github.com/tu-usuario/aws-cat-tool.git](https://github.com/Ssalazarp420/Plataforma-de-traduccion-asistida---AWS/tree/main))
    ```
2.  **Configurar el Backend:**
    * Desplegar las funciones Lambda ubicadas en `/lambda`.
    * Crear las tablas en DynamoDB según la especificación de arquitectura.
3.  **Ejecutar el Frontend:**
    Debido a las políticas de CORS, no abras el `index.html` directamente. Usa Python para servirlo:
    ```bash
    cd frontend
    python -m http.server 8000
    ```
    Accede a `http://localhost:8000` en tu navegador[cite: 1312].

## 🔮 Próximos Pasos (Roadmap)
* [cite_start][ ] Implementar **Amazon Cognito** para autenticación de usuarios[cite: 1323].
* [cite_start][ ] Habilitar método POST en la API para guardar correcciones de traducción[cite: 1324].
* [cite_start][ ] Desplegar el frontend utilizando **S3 Static Website Hosting** para acceso público[cite: 1325].

---
**Estudiante:** [Sebastian Salazar Perez]
[cite_start]**Fecha:** 2 de diciembre de 2025 [cite: 1084]
