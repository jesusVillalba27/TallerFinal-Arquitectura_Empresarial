# Informe de Ingeniería: Arquitectura de Sistemas de Información y Arquitectura Tecnológica
**Caso de Estudio:** Ecosistema Digital de Intermediación Turística – Sucre Conecta Digital S.A.S.  
**Asignatura:** Arquitectura Empresarial  
**Integrantes:** Jesús Villalba, Steven Tete, Jesús Madera, Cristian Brieva  

---

## 1. Introducción y Sustento Estratégico
El presente documento constituye la entrega final y consolidada del diseño de la **Arquitectura de Sistemas de Información (ASI)** y la **Arquitectura Tecnológica (AT)** para la organización **Sucre Conecta Digital S.A.S.** El objetivo de esta fase es materializar las definiciones de negocio previamente estructuradas (Modelo Operativo de 4 Fases y Capacidades Críticas de Gestión de Proveedores, Analítica y Calidad) en una infraestructura tecnológica física y lógica de nivel empresarial. Para ello, se adopta un enfoque basado en computación en la nube bajo el modelo de **Arquitectura Limpia (Clean Architecture)** y **Microservicios Desacoplados**, garantizando que el ecosistema digital sea altamente disponible, seguro, elástico y tolerante a fallos, preparado para mitigar la informalidad y la fragmentación del sector turístico en el departamento de Sucre.

---

## 2. Entregable A: Catálogo Tecnológico Exhaustivo
Este catálogo identifica, clasifica y justifica cada componente de hardware, software e infraestructura cloud requeridos para soportar las operaciones del ecosistema digital, garantizando trazabilidad total con la operación del negocio.

| Nombre del Componente | Función Específica en el Ecosistema | Tecnología Seleccionada | Justificación Técnica y Operativa |
| :--- | :--- | :--- | :--- |
| **Portal de Experiencia del Cliente (Frontend)** | Interfaz de usuario interactiva y responsiva de cara al turista para la exploración de destinos, auto-construcción de itinerarios de viaje y gestión de perfiles de prestadores locales. | **Next.js (React 18) + TailwindCSS + TypeScript** | Next.js permite la generación de sitios estáticos (SSG) y el renderizado del lado del servidor (SSR). Esto es crítico para optimizar el posicionamiento SEO en motores de búsqueda, permitiendo captar turistas nacionales e internacionales de forma orgánica sin sobrecargar el cliente móvil. |
| **Servidor de Aplicación Core (Backend API)** | Orquestación completa de la lógica de negocio: procesamiento de reservas, lógicas de verificación de prestadores rurles, autenticación de usuarios y enrutamiento transaccional. | **Node.js (NestJS Framework) + TypeScript** | NestJS provee una arquitectura empresarial mantenible y modular. Su motor asíncrono basado en eventos (Event-Loop) gestiona de forma eficiente miles de conexiones concurrentes sin bloqueo de Entrada/Salida (I/O), ideal para mitigar cuellos de botella en temporadas altas de festividades. |
| **Motor Analítico y de Recomendación (IA Engine)** | Componente especializado en ejecutar algoritmos de Machine Learning para la personalización de rutas temáticas y modelos predictivos de la demanda turística estacional. | **Python 3.11 + FastAPI + Scikit-Learn** | Python es el estándar de la industria para ciencia de datos. FastAPI expone endpoints de ultra alto rendimiento basados en estándares OpenAPI/Swagger, procesando matrices de datos de comportamiento del turista de manera asíncrona. |
| **Base de Datos Transaccional (Relacional)** | Persistencia de datos de alta fidelidad, asegurando transacciones financieras incorruptibles, registros de usuarios, auditorías de auditoría y estados de pago. | **Amazon RDS (PostgreSQL 16) con Multi-AZ** | PostgreSQL ofrece cumplimiento estricto de las propiedades ACID (Aislamiento, Consistencia, Integridad y Durabilidad). La replicación Multi-AZ en AWS garantiza una conmutación por error automática en menos de 60 segundos si falla un centro de datos físico. |
| **Base de Datos Documental (No Relacional)** | Almacenamiento flexible y de alta lectura para el catálogo dinámico de servicios, características mutables de operadores turísticos, eventos de alcaldías y almacén de reseñas. | **Amazon DocumentDB (Compatible con MongoDB)** | Al ser un esquema libre de documentos JSON, permite agregar o modificar atributos específicos de prestadores rurales (por ejemplo, si un hospedaje añade guianza ecológica) sin alterar la estructura transaccional base ni requerir migraciones complejas de tablas. |
| **Repositorio de Objetos Desacoplado (Storage)** | Almacenamiento masivo y seguro de datos no estructurados, tales como documentos legales de validación (RNT, cédulas) y material multimedia (fotos/videos 4K) de los destinos. | **Amazon S3 (Simple Storage Service)** | S3 ofrece una durabilidad del 99.999999999% mediante replicación automática entre múltiples zonas de disponibilidad, reduciendo costos operativos mediante políticas de ciclo de vida de los datos. |
| **Firewall de Aplicaciones Web (Capa 7)** | Filtro perimetral de seguridad para inspeccionar el tráfico HTTP/HTTPS entrante y bloquear ataques maliciosos antes de que toquen los servidores de cómputo. | **AWS WAF (Web Application Firewall)** | Mitiga activamente las amenazas del OWASP Top 10 (Inyecciones SQL, Cross-Site Scripting) y ataques de denegación de servicio distribuido (DDoS), blindando los activos de información del ecosistema. |
| **Orquestador y Balanceador de Tráfico (Capa 7)** | Distribución uniforme y balanceada de las peticiones de red entrantes hacia los contenedores de aplicación que se encuentren en estado óptimo. | **AWS ALB (Application Load Balancer)** | Realiza enrutamiento avanzado basado en rutas de URL (ej. `/api/v1/*` va al backend, `/analytics/*` va a la IA) y ejecuta Health Checks automáticos para aislar instancias caídas. |
| **Pasarela de Recaudo Financiero (Externa)** | Procesamiento integrado y seguro de cobros mediante tarjetas de crédito, débito, transferencias bancarias de bajo costo (PSE) y corresponsales locales. | **Wompi API / PayU Latam SDK** | Proveedores externos certificados bajo el estándar internacional de máxima seguridad financiera PCI-DSS, indispensable para generar confianza de pago en zonas rurales de Sucre. |
| **Gateway de Mensajería y Alertas (Externo)** | Proveedor de servicios de comunicación omnicanal para el envío automático de comprobantes de reservas, alertas de viaje y códigos de validación OTP. | **Twilio API (SMS) & SendGrid API (Email)** | Infraestructura externa especializada con altas tasas de entregabilidad global, asegurando que un operador rural sin conectividad constante reciba alertas de reservas directamente en su línea móvil tradicional por SMS. |

---

## 3. Entregable B: Diagrama Tecnológico de Flujo de Datos
Este diagrama conceptual modela la secuencia lógica y el flujo de los datos desde que un cliente inicia una petición en internet, atravesando las capas perimetrales, lógicas, analíticas, de persistencia e integraciones de terceros.

```mermaid
graph TD
    subgraph Capa_Usuarios [Actores de Negocio]
        T[Turistas Nacionales / Internacionales]
        P[Prestadores Turísticos Locales]
        A[Administradores de Sucre Conecta / Entes Públicos]
    end
    
    subgraph Capa_Seguridad [Seguridad Perimetral y Enrutamiento]
        DNS[AWS Route 53<br>Puerto: 443 HTTPS]
        WAF[AWS WAF<br>Filtro de Reglas Maliciosas]
        ALB[AWS Application Load Balancer<br>Distribución de Carga]
    end
    
    subgraph Capa_Computo [Capa Lógica y de Sistemas de Información]
        FE[Servicios de Frontend<br>Next.js Framework]
        BE[Core API Gateway y Backend<br>NestJS / Node.js]
        AI[Motor de Inteligencia Artificial<br>Python / FastAPI]
    end
    
    subgraph Capa_Persistencia [Capa de Datos y Almacenamiento]
        RDS[(Amazon RDS<br>PostgreSQL: Datos ACID)]
        DocDB[(Amazon DocumentDB<br>MongoDB JSON: Catálogos)]
        S3[(Amazon S3 Bucket<br>Multimedia y Documentación Legal)]
    end
    
    subgraph Servicios_Externos [APIs e Integraciones de Terceros]
        PAG[Pasarela Financiera<br>Wompi / PayU API]
        NOT[Proveedores de Alertas<br>Twilio & SendGrid API]
    end

    %% Relaciones y Protocolos %%
    T & P & A -->|HTTPS / SSL TLS 1.3| DNS
    DNS -->|Filtrado de Paquetes| WAF
    WAF -->|Petición Limpia| ALB
    ALB -->|Balanceo Round-Robin / Puerto 3000| FE
    FE -->|REST API / GraphQL requests / Puerto 8080| BE
    BE <-->|Llamadas Internas RPC / Puerto 8000| AI
    
    BE -->|Query SQL / Puerto 5432| RDS
    BE -->|Protocolo Wire / Puerto 27017| DocDB
    BE -->|AWS SDK HTTPS / Puerto 443| S3
    AI -->|Lectura de Tendencias / Puerto 27017| DocDB
    
    BE -->|Webhook HTTPS Outbound| PAG
    BE -->|REST HTTPS Outbound| NOT
