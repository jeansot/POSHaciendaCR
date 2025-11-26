# POSHaciendaCR
🏷️ Descripción general del proyecto

POS-CR Hacienda es un sistema moderno, escalable y completamente modular diseñado para negocios pequeños y medianos en Costa Rica (pulperías, panaderías, minisúper, carnicerías, licoreras, etc.).

El sistema permite gestionar productos, ventas, inventario, clientes, caja y reportes, además de generar y enviar comprobantes electrónicos al Ministerio de Hacienda de Costa Rica según el estándar vigente.

El proyecto integra un módulo OCR/IA que permite leer fotos de recibos físicos generados por otros POS, procesarlos y convertirlos en comprobantes electrónicos válidos para Hacienda.

Desarrollado con:

Backend: Node.js + Express + TypeScript + MongoDB

Frontend: Angular + Bootstrap

IA/OCR: Tesseract.js, Google Vision, OCR.Space o AWS Rekognition

Facturación electrónica: Construcción XML, firma criptográfica y envío al API de MH

Este repositorio define la arquitectura oficial, estándares, estructura y roadmap del proyecto.

🧱 Arquitectura del proyecto

El proyecto sigue el estilo de programación estándar de Jean Pierre Soto:

✔ Arquitectura por capas
✔ Responsabilidades separadas (controladores, servicios, modelos)
✔ Servicios sin lógica HTTP
✔ Controladores sin lógica de negocio
✔ Modelos simples sin lógica
✔ Naming conventions estrictas
✔ Código TypeScript limpio y mantenible

🧩 Diagrama general de arquitectura
┌─────────────────────┐            ┌───────────────────────┐
│     Angular SPA      │  HTTP API  │  Node.js + Express    │
│ (POS, Inventario,    │───────────▶│  Backend REST / TS     │
│ Caja, Reportes)      │            │                       │
└─────────────────────┘            │  ┌──────────────────┐  │
                                   │  │ Controllers      │  │
                                   │  ├──────────────────┤  │
                                   │  │ Services (SC)    │  │
                                   │  ├──────────────────┤  │
                                   │  │ Models / Schema  │  │
                                   │  └──────────────────┘  │
                                   │         │               │
                                   │         ▼               │
                                   │   MongoDB Cluster       │
                                   │         │               │
                                   │         ▼               │
                                   │   Hacienda Module       │
                                   │ (XML, Firma, Envío MH)  │
                                   │         │               │
                                   │         ▼               │
                                   │     OCR Module          │
                                   │ (IA / OCR / Parsing)    │
                                   └─────────────────────────┘

📁 Estructura del backend
/src
  /config
      mongo.ts
      env.ts
      hacienda.config.ts
      ocr.config.ts

  /utils
      error.handle.ts
      jwt.handle.ts
      pdf.handle.ts
      xml.builder.ts
      signature.handle.ts

  /middlewares
      auth.ts
      validateSchema.ts
      log.ts

  /interfaces
      user.interface.ts
      product.interface.ts
      sale.interface.ts
      inventory.interface.ts
      client.interface.ts
      cash.interface.ts
      invoice.interface.ts
      ocr.interface.ts

  /models
      user.model.ts
      product.model.ts
      sale.model.ts
      inventory.model.ts
      client.model.ts
      cash.model.ts
      invoice.model.ts
      ocr.model.ts

  /services
      user.service.ts
      product.service.ts
      sale.service.ts
      inventory.service.ts
      client.service.ts
      cash.service.ts
      invoice.service.ts
      hacienda.service.ts
      ocr.service.ts

  /controllers
      user.controller.ts
      product.controller.ts
      sale.controller.ts
      inventory.controller.ts
      client.controller.ts
      cash.controller.ts
      invoice.controller.ts
      hacienda.controller.ts
      ocr.controller.ts

  /routes
      user.ts
      product.ts
      sale.ts
      inventory.ts
      client.ts
      cash.ts
      invoice.ts
      hacienda.ts
      ocr.ts
      index.ts

  app.ts
  server.ts

🗄️ Estructura del frontend (Angular)
/src/app
   /components
       pos/
       products/
       clients/
       inventory/
       cashier/
       reports/
       settings/
       login/

   /services
       api.service.ts
       products.service.ts
       sales.service.ts
       inventory.service.ts
       clients.service.ts
       cash.service.ts
       invoices.service.ts
       hacienda.service.ts
       ocr.service.ts

   /models
       product.model.ts
       sale.model.ts
       client.model.ts
       invoice.model.ts
       ocr.model.ts

   app-routing.module.ts
   app.module.ts

📦 Instalación del backend
1. Clonar el repositorio
git clone https://github.com/usuario/POS-CR-Hacienda.git
cd POS-CR-Hacienda/backend

2. Instalar dependencias
npm install

3. Variables de entorno

Crear .env:

PORT=4000
DB_URI=mongodb://localhost:27017/poscr
JWT_SECRET=secret
HACIENDA_USER=...
HACIENDA_PASS=...
CERTIFICATE_PATH=./certs/llave-cripto.p12
CERTIFICATE_PASSWORD=...
OCR_API_KEY=...


Crear .env.example siguiendo tu estilo.

4. Iniciar en desarrollo
npm run dev

5. Compilar para producción
npm run build
npm start

💼 Instalación del frontend
cd frontend
npm install
ng serve

🔌 Endpoints REST principales
Productos

GET /api/products
POST /api/products
PUT /api/products/:id
DELETE /api/products/:id

Ventas

POST /api/sales

Facturación Electrónica (Hacienda)

POST /api/hacienda/generate-xml
POST /api/hacienda/sign-xml
POST /api/hacienda/send
GET /api/hacienda/status/:clave

OCR (IA)

POST /api/ocr/receipt

Recibe una imagen

Extrae texto

Identifica productos, totales, impuestos

Devuelve JSON listo para construir comprobante

⚙️ Flujo de facturación electrónica

Usuario genera venta POS

Se crea un documento en sales

Si la venta debe enviarse a MH:

Se construye XML (xml.builder.ts)

Se firma el XML (signature.handle.ts)

Se envía al API de MH (hacienda.service.ts)

El sistema recibe respuesta

Guarda estado en invoices

Envía al cliente:

XML

PDF de representación gráfica

🤖 Flujo OCR / IA

El usuario sube una foto del recibo físico

El backend recibe la imagen /api/ocr/receipt

OCRService extrae texto

Parser identifica:

Líneas

Cantidades

Precios

Totales

El sistema devuelve JSON a Angular

Angular muestra la factura “normalizada”

El usuario confirma y genera la factura electrónica

Se envía a Hacienda igual que cualquier comprobante

📈 Roadmap del proyecto
Fase 1 – Core POS (Versión Local)

Gestión de productos

Inventario (entradas, ajustes)

Ventas

Caja (apertura/cierre)

Reportes básicos

Impresión de tiquete

Fase 2 – Facturación Electrónica (Hacienda)

Módulo XML builder

Firma criptográfica

Envío al API de Hacienda

Manejo de estados (pendiente, aceptado, rechazado)

Representación gráfica en PDF

Envío por correo

Fase 3 – OCR / IA

Subida y lectura de imágenes

OCR + parsing

Normalización de productos e impuestos

Conversión a venta interna

Generación de comprobante electrónico

Fase 4 – Avanzado / Empresarial

Multi empresa

Multi sucursal

Integración contable

Dashboard analítico (ventas, categorías, tendencias)

Exportación avanzada (Excel, reportes fiscales)

🧠 Estilo de programación aplicado

Arquitectura por capas

Services con sufijo SC

Controladores sin lógica de negocio

Modelos sin lógica adicional

handleHttp para errores

TypeScript estricto

Angular con formularios reactivos

Naming convention consistente

No lógica en el HTML

.env.example obligatorio

No duplicar rutas ni endpoints

🎯 Estado del proyecto

Este README define la versión 1.0 de la arquitectura oficial del proyecto POS-CR Hacienda.
Los siguientes pasos serán:

✔ Crear la estructura del backend (carpetas + archivos vacíos)
✔ Crear la estructura del frontend
✔ Implementar módulos base (products, clients, sales)
✔ Preparar XML builder + firma
✔ Integrar OCR
