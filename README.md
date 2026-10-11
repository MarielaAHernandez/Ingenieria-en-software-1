# Sistema de Control de Inventario

Sistema de control de inventario con arquitectura híbrida (offline-first) para optimizar la recepción, la trazabilidad y la gestión de stock en tiempo real.

---

## Objetivo

Centralizar y automatizar el control de la mercancía del almacén, permitiendo conocer en todo momento cuánto stock hay, dónde está ubicado y qué movimientos ha tenido, incluso cuando no hay conexión a internet.

## Problema que resuelve

El control manual del inventario genera una falta total de visibilidad y precisión en el stock, lo que provoca pérdidas económicas por extravíos, graves retrasos en la preparación y confirmación de pedidos al no saber la ubicación o el movimiento de los productos, e imposibilita la toma de decisiones acertadas de compra al carecer de datos confiables y en tiempo real.

El sistema resuelve esto con un registro único de movimientos, consultas de stock en tiempo real, ubicación de la mercancía en el almacén, reportes automáticos y un modo de operación offline con sincronización posterior.

---

## Funcionalidades principales

Basadas en las historias de usuario del proyecto:

| ID | Funcionalidad |
|----|---------------|
| HU-01 | Consulta de stock disponible en tiempo real |
| HU-02 | Consulta de precios actualizados por producto |
| HU-03 | Visualización de ubicaciones de mercancía en almacén |
| HU-04 | Generación de reportes automáticos de stock mínimo y movimientos |
| HU-05 | Registro e historial trazable de movimientos de inventario |
| HU-06 | Registro de pedidos |

### Módulos del sistema

* **Módulo de Seguridad:** autenticación de usuarios y gestión de permisos.
* **Módulo de Procesos:** entradas/salidas, recepciones, devoluciones, transferencias entre sucursales y alertas de stock bajo.
* **Módulo de Mantenimientos:** catálogos de artículos, marcas, modelos, almacenes, categorías y estados (disponible, reservado, en tránsito).
* **Módulo de Consultas e Informes:** existencias en tiempo real, trazabilidad de historial y reportes automáticos.
* **Servicio de Sincronización:** agente en segundo plano que replica datos en contingencias u operación offline.

---

## Arquitectura seleccionada

**Arquitectura en capas (Layered Architecture)** con enfoque **híbrido / offline-first**.

| Capa | Responsabilidad |
|------|-----------------|
| **Presentación** | Interfaz de escritorio (.NET 8): formularios, validaciones básicas y visualización de datos. |
| **API / Servicios** | .NET 8 Web API: expone los endpoints, autentica y autoriza las peticiones. |
| **Lógica de negocio** | Reglas del inventario: cálculo de stock, validación de movimientos, alertas de stock mínimo. |
| **Acceso a datos** | Repositorios que leen y escriben en la base de datos relacional. |
| **Datos** | SQL Server (servidor local) con respaldo en la nube. |

Cada capa solo se comunica con la inmediatamente inferior, lo que facilita el mantenimiento, las pruebas y la evolución del sistema.

---

## Organización Frontend / Backend

### Frontend (aplicación de escritorio, .NET 8)

**Responsabilidades:**

* Mostrar al usuario el stock, precios, ubicaciones y reportes.
* Capturar los datos de entradas, salidas, pedidos, devoluciones y transferencias.
* Validar los datos en pantalla antes de enviarlos (campos obligatorios, formatos).
* Manejar la sesión del usuario y mostrar solo las opciones permitidas por su rol.
* Mostrar el estado de conexión y guardar las operaciones pendientes cuando se trabaja sin conexión.

### Backend (.NET 8 Web API)

**Responsabilidades:**

* Autenticar usuarios y controlar permisos por rol.
* Aplicar las reglas de negocio (no permitir salidas mayores al stock, generar alertas de stock mínimo, etc.).
* Registrar cada movimiento de inventario con usuario, fecha y tipo para garantizar la trazabilidad.
* Gestionar el acceso a la base de datos SQL Server.
* Generar los reportes automáticos.
* Ejecutar el servicio de sincronización entre el servidor local y el respaldo en la nube.
