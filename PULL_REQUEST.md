# Pull Request: Implementación de Módulos de Pedidos, Gestión de Stock y Fidelización (HU-04, HU-05, HU-06)

## Descripción

Este Pull Request introduce la lógica central de la aplicación de cafetería (`app.js`), que conecta el flujo de estudiantes con el panel de administración. Abarca desde la creación de pedidos hasta el cobro en físico, la acumulación de sellos por la compra de sándwiches y la validación en tiempo real del stock de los productos.

---

## Historias de Usuario Implementadas

### 🥪 [HU-04] Flujo de Pedidos y Cobro Físico
* Se implementó el ciclo de vida del pedido: `pending` (en preparación) → `ready` (listo para recoger) → `completed` (entregado y cobrado).
* Los pedidos son guardados en `localStorage` con un identificador de turno correlativo.
* En el panel administrativo, el dependiente puede cambiar el estado a "Listo" y posteriormente realizar la entrega marcando el botón **"Cobrar y Entregar"**, asegurando la transacción física del dinero.

### 🎯 [HU-05] Sistema de Fidelización por Sellos
* Al completarse una orden (`completeOrder`), el sistema analiza los items del pedido.
* Por cada sándwich entregado (identificado por la palabra clave en el nombre del producto), se suma un sello al código del estudiante correspondiente.
* El total de sellos se guarda en `localStorage` bajo el objeto `ingenio_stamps` y se visualiza en tiempo real en la barra de progreso del cliente, mostrando una recompensa especial al acumular 10 sellos.

### 🚫 [HU-06] Control de Stock y Deshabilitación de Productos
* Permite al administrador deshabilitar productos agotados temporalmente usando interruptores (*toggles*).
* **Validación del Carrito:** Si un estudiante tiene en su carrito un producto que el administrador acaba de deshabilitar, el sistema remueve automáticamente el producto del carrito y muestra un mensaje de advertencia (*toast*).
* **Validación al Ordenar:** Antes de registrar la orden, el sistema vuelve a verificar el estado activo de todos los productos en el carrito para prevenir pedidos falsos de última hora.

---

## Cambios Principales en `app.js`

* **Manejo del Estado:** Funciones de utilidad `getProducts()`, `saveProducts()`, `getOrders()`, `saveOrders()`, `getStamps()` y `saveStamps()` para sincronizar datos con el almacenamiento local del navegador (`localStorage`).
* **Validaciones de Entrada:** Expresión regular `/^\d{10}[a-zA-Z]$/` para asegurar que el código estudiantil ingresado coincida exactamente con la estructura institucional requerida (10 dígitos seguidos de una letra).
* **Interacciones Dinámicas:** Funciones globales como `addToCart()`, `removeFromCart()`, `toggleProductStatus()`, `markOrderReady()` y `completeOrder()` para modificar el estado y refrescar el DOM.

---

## Guía de Pruebas y Verificación

1. **Simulación de Flujo Estudiantil:**
   * Ingresa a la interfaz principal y añade productos al carrito.
   * Modifica o ingresa un código inválido para comprobar que el botón de pedido se bloquea. Ingresa un código correcto (ej: `2026101454E`) para habilitarlo.
   * Realiza el pedido y comprueba la generación del ticket de compra.

2. **Simulación de Stock (HU-06):**
   * Añade un *Sándwich de Pollo* al carrito en el perfil de estudiante.
   * Entra al panel de administración (contraseña: `julio123`) y desactiva el *Sándwich de Pollo*.
   * Regresa al perfil de estudiante y verifica la alerta de producto removido.

3. **Flujo de Pedido y Sellos (HU-04 y HU-05):**
   * Haz un pedido que contenga 2 sándwiches.
   * En el administrador, marca el pedido como **"Listo"** y luego **"Cobrar y Entregar"**.
   * Consulta el estado del código en la sección de cliente para verificar que ahora tienes **2 sellos** acumulados.
