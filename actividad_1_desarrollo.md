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

| sitod | Historia                                                                                                                     | Criterios de aceptacion                                                                                                                                                                        | invest |
|-------|------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| US-01 | Como cliente, quiero poder hacer pedidos, para comprar los productos seleccionados                                           | 1- Debe genrar un id unico de pedido 2 - el stock de cada producto se descuenta 3 - No se puede haver un pedido con productos nos disponibles 4 - El estado inicial del producto es “aprobado” |        |
| US-02 | Como cliente quiero poder consultar productos, con precio y disponibilidad                                                   | 1 - El sistema muestra nombre, precio y stock disponible 2 - Si un producto no tiene existencias se muestra fcomo “sin disponibilidad” 3 - tiempo de respuesta ≤ 300 ms                        |        |
| US-03 | Como cliente quiero poder acceder a la informacion de mis pedido, saber donde esta, en que estado esta mi pedido.            | 1 - Se muestra el estado actual del producto(aprobado, Empacado, en ruta, entregado, cancelado) 2 - actualizacion de estado maximo cada 30s. 3 - Solo puede ver sus propios pedidos            |        |
| US-04 | Como Administrador, quiero poder gentionar el inventario, añadir o desactivar productos, cambiar aspectos de los productos   | 1 - Solo usuarios con rol admin pueden acceder a editar (debe autenticarse por AWT tokens) 2 - Permite editar y crear productos con precio y cantidad 3 - No puede haber stock negativo        |        |
| US-05 | Como repartidor quiero ver los productos que se me han asignado                                                              | 1 - se debe mostrar la informacion del pedido 2 - puede actualizar el estado del producto (en ruta o entregado) 3 - debe ver la direccion de entrega del pedido.                               |        |
| US-06 | Como operador de enregas, quiero asignarle pedidos a reapartidores disponibles, para organizar las rutas de manera eficiente | 1 - El operador puede asignar pedidos 2 - Se registra la fecha y hora de asignacion 3 - El pedido cambia automaticamente a asignado.                                                           |        |
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


