<div align="center">

# 🌐 Plataforma de traducción inteligente serverless
### Proyecto AWS #27 - (Integración con IA)

</div>

## 📋 Descripción
Este proyecto es una evolución de la herramienta CAT (Computer-Assisted Translation) implementada sobre una arquitectura **Serverless** en AWS. A diferencia de prototipos anteriores, esta versión integra **Inteligencia Artificial** para digitalizar y traducir documentos automáticamente.

El sistema permite subir archivos (tanto `.txt` como `.pdf`), extrae su contenido mediante OCR inteligente, lo traduce utilizando redes neuronales y notifica al usuario vía email cuando el proceso finaliza.

👉 **Demo:** [Acceder al sitio web de prueba](http://plataforma-traduccion-pdfs.s3-website.us-east-2.amazonaws.com)

---

## 🏗️ Arquitectura
El flujo de datos sigue un modelo reactivo (Event-Driven) iniciado por la carga de archivos:

<div align="center">
  <img src="readme-images/AWS_diagram_v2.png" alt="Diagrama de Arquitectura" width="70%">
</div>

### Flujo de Trabajo Actualizado:
1.  **Ingesta:** El usuario sube un archivo a **S3** (`uploads/`). Soporta formatos `.txt` y `.pdf`.
2.  **Detección y Extracción:** Una función **Lambda** detecta el formato:
    * *Si es PDF:* Invoca a **Amazon Textract** para realizar OCR y extraer texto plano.
    * *Si es TXT:* Lee el contenido directamente de S3.
3.  **Traducción Neuronal:** El texto segmentado se envía a **Amazon Translate** (servicio real, sin simulaciones) para traducir del inglés al español.
4.  **Persistencia:** Los pares de segmentos (origen-destino) se almacenan en **DynamoDB**.
5.  **Notificación:** Al finalizar, **Amazon SNS** envía un correo electrónico al administrador avisando que el documento está listo.
6.  **Visualización:** El Frontend consulta los resultados a través de **API Gateway**.

### Stack Tecnológico:
* **Compute:** AWS Lambda (Python 3.9).
* **AI/ML:**
    * *Amazon Textract:* OCR para PDFs.
    * *Amazon Translate:* Traducción automática.
* **Storage:** Amazon S3.
* **Database:** Amazon DynamoDB (Tablas On-Demand).
* **Messaging:** Amazon SNS (Notificaciones Email).
* **API:** Amazon API Gateway.

---

## ⚙️ Configuración y Desafíos Técnicos

### Permisos IAM
Para el correcto funcionamiento de la v2, el Rol de Ejecución de Lambda requiere las siguientes políticas gestionadas:
* `AmazonTextractFullAccess`
* `TranslateFullAccess`
* `AmazonSNSFullAccess`

### Soluciones a Problemas (Bitácora v2)
Durante la implementación de la Fase 2, se superaron los siguientes retos técnicos:

| Reto Técnico | Contexto | Solución Implementada |
| :--- | :--- | :--- |
| **Procesamiento de PDF** | S3 no permite leer texto de binarios directamente. | Se integró **Amazon Textract** para extraer el texto antes de traducir. |
| **Permisos de IA** | Error `AccessDenied` al invocar `translate_text`. | Se actualizaron las políticas IAM del LabRole para permitir acceso a servicios de IA. |
| **Notificaciones Asíncronas** | Necesidad de aviso al terminar proceso pesado. | Implementación de **Amazon SNS** al final del flujo de la Lambda. |
| **Serialización JSON** | Error con tipos `Decimal` de DynamoDB en la API. | Implementación de clase `DecimalEncoder` en el handler de la API. |

---

## 🚀 Instalación y Uso

1.  **Configurar SNS:**
    * Crear un Tópico en SNS (`TranslationAlerts`) y suscribir tu email.
    * **Importante:** Confirma la suscripción en el enlace que llegará a tu correo.
2.  **Despliegue:**
    * Actualizar la variable `TOPIC_ARN` en el código de la Lambda con el ARN de tu tópico SNS.
    * Subir el código actualizado (`lambda_function.py`) que incluye la lógica de Textract y Translate.
3.  **Prueba:**
    * Sube un archivo PDF en inglés a la carpeta `uploads/` del bucket S3.
    * Espera el correo de notificación de SNS.
    * Verifica la traducción en el Frontend web.

---
**Arquitecto Cloud:** Sebastian Salazar Perez
<br>
**Fecha:** 3 de diciembre de 2025
