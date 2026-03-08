# Documento de Arquitectura del Sistema de Gestión de Órdenes y Entregas  

## 1. Introducción  
Este documento describe la arquitectura inicial del sistema de gestión de órdenes y entregas, incluyendo requisitos funcionales, requisitos de calidad y restricciones que deben ser consideradas en el diseño del software.

El sistema se implementará bajo una arquitectura de microservicios, con un API Gateway, mensajería asíncrona y despliegue en contenedores, garantizando escalabilidad, disponibilidad, seguridad y trazabilidad.

**Equipo:** Equipo Ágiles TI  
**Integrantes:** [Juan Jose Valencia, Tatiana Rengifo, Pedro Jose Lopez, Dilan Mauricio Lemos, Jaime Noreña, Diego Lenis, Juan Jose Restrepo]  
**Fecha:** 01/03/2026  

---

## 2. Requisitos Funcionales  

Los requisitos funcionales se presentan en forma de historias de usuario con criterios de aceptación verificables.

### Historias de Usuario

| sitod | Historia | Criterios de aceptacion | invest |
|-------|----------|-------------------------|--------|
| US-01 | Como cliente, quiero poder realizar pedidos para comprar los productos seleccionados | 1 - El sistema genera un ID único para cada pedido. 2 - El sistema valida la disponibilidad de stock antes de confirmar el pedido. 3 - No se puede crear un pedido con productos no disponibles. 4 - Al crearse el pedido, su estado inicial es “aprobado”. 5 - El stock de cada producto se descuenta automáticamente al confirmarse el pedido. | Cumple INVEST: independiente, negociable, valiosa para el usuario, estimable, pequeña y verificable. |
| US-02 | Como cliente quiero poder consultar productos con su precio y disponibilidad | 1 - El sistema muestra el nombre del producto, precio y stock disponible. 2 - Si un producto no tiene existencias se muestra como “sin disponibilidad”. | Cumple INVEST: independiente de otras historias, aporta valor directo al cliente, es estimable y verificable. |
| US-03 | Como cliente quiero poder acceder a la información de mis pedidos para saber en qué estado se encuentran | 1 - Se muestra el estado actual del pedido (aprobado, empacado, en ruta, entregado, cancelado). 2 - Solo el cliente propietario del pedido puede visualizarlo. 3 - El cliente puede consultar el historial de estados del pedido. | Cumple INVEST: independiente, con valor claro para el cliente, verificable mediante pruebas funcionales. |
| US-04 | Como administrador, quiero poder gestionar el inventario para añadir, modificar o desactivar productos | 1 - Solo usuarios con rol administrador pueden acceder a la gestión de inventario. 2 - La autenticación se realiza mediante tokens JWT. 3 - El administrador puede crear, editar o desactivar productos. 4 - No puede registrarse stock negativo en el sistema. | Cumple INVEST: funcionalidad clara, independiente y verificable mediante control de roles. |
| US-05 | Como repartidor quiero ver los pedidos que se me han asignado para poder realizar las entregas | 1 - El sistema muestra la información del pedido asignado. 2 - El repartidor puede actualizar el estado del pedido a “en ruta” o “entregado”. 3 - El repartidor puede visualizar la dirección de entrega del pedido. | Cumple INVEST: funcionalidad pequeña, clara y verificable. |
| US-06 | Como operador de entregas, quiero asignar pedidos a repartidores disponibles para organizar las rutas de entrega | 1 - El operador puede asignar pedidos a repartidores disponibles. 2 - El sistema registra la fecha y hora de asignación del pedido. 3 - Al asignarse un pedido, su estado cambia automáticamente a “asignado”. | Cumple INVEST: historia independiente, con valor operativo y criterios verificables. |

---

## 3. Requisitos de Calidad  

Los requisitos de calidad se presentan como escenarios medibles.

### Historias de Calidad

| ID | Fuente | Estímulo | Artefactos | Entorno | Respuesta | Medida de Respuesta |
|----|--------|----------|------------|----------|------------|----------------------|
| RQ-01 | Usuario | Falla de una instancia | Microservicios en Kubernetes | Operación normal | El sistema redirige el tráfico a instancias saludables | Disponibilidad ≥ 99.5% mensual |
| RQ-02 | Cliente | Consulta del catálogo de productos | Servicio de catálogo | Hora pico | El sistema responde rápidamente a la solicitud | 95% de solicitudes ≤ 300 ms |
| RQ-03 | Sistema | Aumento sostenido de carga | Clúster Kubernetes | Alta demanda | El sistema realiza escalamiento automático de instancias | Nuevas instancias en < 2 minutos |
| RQ-04 | Usuario malicioso | 5 intentos fallidos de inicio de sesión | Servicio de autenticación | Operación normal | El sistema bloquea temporalmente la cuenta y genera una alerta | Bloqueo tras 5 intentos fallidos |
| RQ-05 | Broker de mensajería | Falla en el procesamiento de un mensaje | Kafka/RabbitMQ | Envío de evento | El sistema reintenta el procesamiento o envía el mensaje a una Dead Letter Queue | 0 pérdida de mensajes |
| RQ-06 | Sistema | Error repetitivo en los servicios | Sistema de logs centralizado | Operación normal | Se genera una alerta automática para el equipo de operaciones | Alerta generada en < 1 minuto |
| RQ-07 | Sistema | Confirmación de pedido | Servicio de inventario | Transacciones concurrentes | El sistema mantiene la consistencia de los datos evitando stock negativo | 0 registros con stock < 0 |
| RQ-08 | Repartidor | Cambio de estado del pedido | Servicio de entregas | Pedido en ruta | El sistema actualiza el estado visible para el cliente | Estado reflejado en ≤ 30 segundos |

---

## 4. Restricciones del Sistema  

Las restricciones se derivan directamente del relato proporcionado.

| Tipo de Restricción | Descripción |
|---------------------|------------|
| Tecnológica | Implementación obligatoria con Spring Boot y arquitectura de microservicios. |
| Tecnológica | Uso de API Gateway (Spring Cloud Gateway o Kong). |
| Seguridad | Autenticación mediante OAuth2 y JWT. |
| Infraestructura | Despliegue obligatorio en Kubernetes. |
| Integración | Mensajería asíncrona con Kafka o RabbitMQ. |
| Calidad | Tiempo de respuesta máximo de 300 ms en servicios críticos. |

---

## 5. Conclusión  

El sistema propuesto responde a las necesidades del negocio mediante una arquitectura moderna basada en microservicios, asegurando:

- Alta disponibilidad  
- Escalabilidad automática  
- Seguridad robusta  
- Observabilidad y trazabilidad  
- Experiencia consistente para clientes, administración y reparto
