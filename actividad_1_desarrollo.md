# Documento de Arquitectura del Sistema de Gestión de Órdenes y Entregas  

## 1. Introducción  
Este documento describe la arquitectura inicial del sistema de gestión de órdenes y entregas, incluyendo requisitos funcionales, requisitos de calidad y restricciones que deben ser consideradas en el diseño del software.

El sistema se implementará bajo una arquitectura de microservicios, con un API Gateway, mensajería asíncrona y despliegue en contenedores, garantizando escalabilidad, disponibilidad, seguridad y trazabilidad.

**Equipo:** Equipo Ágiles TI  
**Integrantes:** [Juan Jose Valencia, Tatianan Rengifo, Pedro Jose Lopez, Dilan Mauricio, Jaime Noreña, Diego, Juan Jose Restrepo]  
**Fecha:** 01/03/2026  

---

## 2. Requisitos Funcionales  

Los requisitos funcionales se presentan en forma de historias de usuario con criterios de aceptación verificables.

### Historias de Usuario

| ID | Historia de Usuario | Criterios de Aceptación | INVEST |
|----|---------------------|------------------------|--------|
| US-01 | Como cliente, quiero consultar el catálogo de productos con precio y disponibilidad, para decidir qué comprar. | 1. El sistema muestra nombre, precio y stock disponible.<br>2. Si un producto está agotado, debe mostrarse como “Sin disponibilidad”.<br>3. Tiempo de respuesta menor o igual a 300 ms en condiciones normales. | Cumple INVEST: independiente, valiosa, pequeña y testeable. |
| US-02 | Como cliente, quiero realizar un pedido con pago simulado, para comprar productos seleccionados. | 1. Se genera un ID único de pedido.<br>2. El stock se descuenta automáticamente.<br>3. No se permite confirmar si no hay stock suficiente.<br>4. El estado inicial es “Aprobado”. | Cumple INVEST. Puede dividirse si aumenta su complejidad. |
| US-03 | Como cliente, quiero consultar el estado de mi pedido, para saber en qué etapa se encuentra. | 1. Estados visibles: Aprobado, Empacado, En ruta, Entregado, Cancelado.<br>2. Actualización en máximo 30 segundos cuando esté en ruta.<br>3. Solo puede ver sus propios pedidos (validación por JWT). | Cumple INVEST. Clara y verificable. |
| US-04 | Como administrador, quiero gestionar el inventario (crear, actualizar y ajustar stock), para mantener existencias correctas. | 1. Permite crear y editar productos.<br>2. No permite stock negativo.<br>3. Solo usuarios con rol ADMIN pueden acceder. | Cumple INVEST. |
| US-05 | Como repartidor, quiero ver los pedidos asignados, para organizar mis entregas. | 1. Solo ve pedidos asignados a su usuario.<br>2. Puede cambiar estado a “En ruta” y “Entregado”.<br>3. Puede registrar novedades. | Cumple INVEST. |
| US-06 | Como operador de entregas, quiero asignar pedidos a repartidores, para organizar las entregas eficientemente. | 1. Solo usuarios con rol OPERADOR pueden asignar.<br>2. El sistema registra fecha y hora de asignación.<br>3. El pedido cambia a estado “Asignado”. | Cumple INVEST. |

---

## 3. Requisitos de Calidad  

Los requisitos de calidad se presentan como escenarios medibles.

### Historias de Calidad

| ID | Fuente | Estímulo | Artefactos | Entorno | Respuesta | Medida de Respuesta |
|----|--------|----------|------------|----------|------------|----------------------|
| RQ-01 | Usuario | Falla de una instancia | Microservicios en Kubernetes | Operación normal | Redirige tráfico a instancias sanas | Disponibilidad ≥ 99.5% mensual |
| RQ-02 | Cliente | Consulta de catálogo | Servicio de catálogo | Hora pico | Responde ágilmente | 95% de solicitudes ≤ 300 ms |
| RQ-03 | Sistema | Aumento sostenido de carga | Clúster Kubernetes | Alta demanda | Escalamiento automático | Nuevas instancias en < 2 min |
| RQ-04 | Usuario malicioso | 5 intentos fallidos de login | Servicio de autenticación | Operación normal | Bloqueo temporal + alerta | Bloqueo tras 5 intentos |
| RQ-05 | Broker | Falla en procesamiento de mensaje | Kafka/RabbitMQ | Envío de evento | Reintento automático o Dead Letter Queue | 0 pérdida de mensajes |
| RQ-06 | Sistema | Error repetitivo | Logs centralizados | Operación normal | Genera alerta automática | Alerta en < 1 minuto |
| RQ-07 | Pedido confirmado | Descuento de stock | Servicio inventario | Transacciones concurrentes | Evita stock negativo | 0 registros con stock < 0 |
| RQ-08 | Repartidor | Cambio de estado | Servicio entregas | Pedido en ruta | Actualiza estado visible al cliente | Reflejado en ≤ 30 segundos |

---

## 4. Restricciones del Sistema  

Las restricciones se derivan directamente del relato proporcionado.

| Tipo de Restricción | Descripción |
|---------------------|------------|
| Tecnológica | Implementación obligatoria con Spring Boot y arquitectura de microservicios. |
| Tecnológica | Uso de API Gateway (Spring Cloud Gateway o Kong). |
| Seguridad | Autenticación con OAuth2 y JWT. |
| Infraestructura | Despliegue obligatorio en Kubernetes. |
| Integración | Mensajería asíncrona con Kafka o RabbitMQ. |
| Calidad | Tiempo de respuesta máximo de 300 ms en servicios críticos. |
| Proceso | Metodología SCRUM con sprints de 2 semanas durante 14 semanas. |
| Herramientas | CI/CD con GitHub Actions, pruebas con JUnit, Mockito y Testcontainers, monitoreo con Prometheus y Grafana. |

---

## 5. Conclusión  

El sistema propuesto responde a las necesidades del negocio mediante una arquitectura moderna basada en microservicios, asegurando:

- Alta disponibilidad  
- Escalabilidad automática  
- Seguridad robusta  
- Observabilidad y trazabilidad  
- Experiencia consistente para clientes, administración y reparto  

