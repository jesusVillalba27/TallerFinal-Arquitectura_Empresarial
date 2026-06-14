# Informe de Ingeniería: Arquitectura de Sistemas de Información y Arquitectura Tecnológica

## Caso de Estudio
**Ecosistema Digital de Intermediación Turística – Sucre Conecta Digital S.A.S.**

**Asignatura:** Arquitectura Empresarial  
**Integrantes:** Jesús Villalba, Steven Tete, Jesús Madera, Cristian Brieva

---

# 1. Introducción y Sustento Estratégico

El presente documento constituye el informe técnico consolidado del diseño de la **Arquitectura de Sistemas de Información (ASI)** y la **Arquitectura Tecnológica (AT)** para la organización **Sucre Conecta Digital S.A.S.**

El objetivo fundamental de esta fase es materializar las definiciones de negocio previamente estructuradas (Modelo Operativo de 4 Fases y Capacidades Críticas de Gestión de Proveedores, Analítica y Calidad) en una infraestructura tecnológica física y lógica de nivel empresarial.

Para ello, se adopta un enfoque basado en computación en la nube bajo el modelo de **Arquitectura Limpia (Clean Architecture)** y **Microservicios Desacoplados**, garantizando que el ecosistema digital sea altamente disponible, seguro, elástico y tolerante a fallos, preparado para mitigar la informalidad y la fragmentación del sector turístico en el departamento de Sucre.

---

# 2. Entregable A: Catálogo Tecnológico Exhaustivo

Este catálogo identifica, clasifica y justifica cada componente de hardware, software e infraestructura cloud requeridos para soportar las operaciones del ecosistema digital.

| Componente | Función | Tecnología Seleccionada | Justificación |
|-------------|---------|-------------------------|---------------|
| **Portal de Experiencia del Cliente (Frontend)** | Interfaz para turistas y prestadores | **Next.js (React 18) + TailwindCSS + TypeScript** | Optimización SEO mediante SSG y SSR. |
| **Servidor de Aplicación Core (Backend API)** | Lógica de negocio y procesamiento de reservas | **Node.js (NestJS) + TypeScript** | Arquitectura modular y procesamiento asíncrono eficiente. |
| **Motor Analítico y de Recomendación (IA Engine)** | Modelos de Machine Learning y predicción | **Python 3.11 + FastAPI + Scikit-Learn** | Alto rendimiento y estándar para ciencia de datos. |
| **Base de Datos Transaccional** | Persistencia de datos críticos | **Amazon RDS PostgreSQL 16 (Multi-AZ)** | Cumplimiento ACID y alta disponibilidad. |
| **Base de Datos Documental** | Catálogos y datos flexibles | **Amazon DocumentDB (MongoDB)** | Esquema flexible basado en JSON. |
| **Repositorio de Objetos** | Almacenamiento de documentos y multimedia | **Amazon S3** | Durabilidad del 99.999999999 %. |
| **Firewall Web** | Protección frente a ataques | **AWS WAF** | Mitigación de amenazas OWASP Top 10. |
| **Balanceador de Carga** | Distribución de tráfico | **AWS Application Load Balancer** | Enrutamiento inteligente y Health Checks. |
| **Pasarela de Pagos** | Procesamiento de pagos | **Wompi API / PayU SDK** | Cumplimiento PCI-DSS. |
| **Gateway de Mensajería** | Notificaciones y alertas | **Twilio API y SendGrid API** | Alta disponibilidad y entregabilidad. |

---

# 3. Entregable B: Diagrama Tecnológico de Flujo de Datos

```mermaid
graph TD

subgraph Capa_Usuarios
T[Turistas]
P[Prestadores Turísticos]
A[Administradores]
end

subgraph Capa_Seguridad
DNS[AWS Route 53]
WAF[AWS WAF]
ALB[AWS Application Load Balancer]
end

subgraph Capa_Computo
FE[Frontend<br>Next.js]
BE[Backend<br>NestJS]
AI[Motor IA<br>Python FastAPI]
end

subgraph Capa_Datos
RDS[(Amazon RDS PostgreSQL)]
DOC[(Amazon DocumentDB)]
S3[(Amazon S3)]
end

subgraph Servicios_Externos
PAG[Wompi / PayU]
NOT[Twilio / SendGrid]
end

T --> DNS
P --> DNS
A --> DNS

DNS --> WAF
WAF --> ALB
ALB --> FE
FE --> BE
BE <--> AI

BE --> RDS
BE --> DOC
BE --> S3
AI --> DOC

BE --> PAG
BE --> NOT
```

---

# 4. Entregable C: Diagrama de Despliegue de Infraestructura Cloud (AWS)

```mermaid
graph TB

subgraph AWS_Cloud["AWS Cloud - us-east-1"]

subgraph VPC["Amazon VPC 10.0.0.0/16"]

subgraph Publicas["Subredes Públicas"]
ALB[AWS Load Balancer]
NAT[NAT Gateway]
end

subgraph Aplicaciones["Subredes Privadas de Aplicación"]

subgraph ECS["Amazon ECS Fargate"]
FE[Contenedor Next.js]
BE[Contenedor NestJS]
AI[Contenedor Python ML]
end

end

subgraph Datos["Subredes Privadas de Datos"]
RDS1[(RDS PostgreSQL Primario)]
RDS2[(RDS PostgreSQL Réplica)]

DOC1[(DocumentDB Principal)]
DOC2[(DocumentDB Réplica)]
end

end

S3[(Amazon S3)]

end

subgraph Clientes
NAV[Browser Web/Mobile]
end

subgraph Servicios_Externos
PAY[Wompi / PayU]
MSG[Twilio / SendGrid]
end

NAV --> ALB

ALB --> FE
FE --> BE
BE --> AI

BE --> RDS1
RDS1 -. Replicación .-> RDS2

BE --> DOC1
DOC1 -. Replicación .-> DOC2

AI --> DOC2

BE --> S3
BE --> PAY
BE --> MSG
```

---

# 5. Entregable D: Justificación de las Decisiones Tecnológicas

## 1. Elasticidad frente a la Estacionalidad

La demanda turística presenta variaciones importantes según las temporadas vacacionales y festividades.

La utilización de **Amazon ECS con AWS Fargate** permite una infraestructura serverless capaz de escalar automáticamente según la carga del sistema, optimizando costos y garantizando disponibilidad.

---

## 2. Persistencia Políglota

El sistema maneja dos tipos de datos:

### Datos transaccionales
- Reservas.
- Pagos.
- Comisiones.

Se emplea **Amazon RDS PostgreSQL**, garantizando propiedades ACID.

### Datos de catálogo

- Prestadores turísticos.
- Servicios.
- Características variables.

Se utiliza **Amazon DocumentDB**, permitiendo esquemas flexibles basados en JSON.

---

## 3. Desacoplamiento Analítico

El motor de Inteligencia Artificial se despliega como un microservicio independiente:

- **Backend principal:** NestJS.
- **Motor analítico:** Python + FastAPI.

Esto evita que las cargas de Machine Learning afecten los procesos críticos de reserva y pagos.

---

## 4. Seguridad y Aislamiento

La arquitectura sigue el principio **Security by Design**:

- AWS WAF como capa perimetral.
- Application Load Balancer como punto de entrada.
- Bases de datos en subredes privadas.
- Sin direcciones IP públicas.
- Acceso a servicios externos mediante NAT Gateway.

Esta estrategia minimiza la superficie de ataque y protege la información sensible de turistas y prestadores de servicios.

---

# Arquitectura Implementada

- **Frontend:** Next.js + TypeScript + TailwindCSS.
- **Backend:** NestJS + Node.js.
- **Motor IA:** Python + FastAPI.
- **Base de datos relacional:** PostgreSQL.
- **Base de datos documental:** MongoDB (DocumentDB).
- **Almacenamiento:** Amazon S3.
- **Infraestructura:** AWS ECS Fargate.
- **Seguridad:** AWS WAF + ALB.
- **Pagos:** Wompi / PayU.
- **Notificaciones:** Twilio + SendGrid.
