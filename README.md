# 📦 Stock - Sistema de Digitalización y Control de Inventarios

![Version](https://img.shields.io/badge/version-2.0.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)

> **Escanea → Registra → Controla → Factura.** Una herramienta todo-en-uno para gestionar inventarios con escáner de códigos de barras, generación de etiquetas, precios y base de datos local.

---

## 🎯 **¿Qué es Stock?**

Stock es una aplicación web **sin servidor** que te permite:

- 📸 **Escáner integrado** (cámara) para lecturas instantáneas de códigos de barras (ISBN, EAN, UPC, CODE128)
- 📚 **Gestión completa** de inventario (CRUD: Crear, Leer, Actualizar, Eliminar)
- 🏷️ **Generación profesional** de códigos de barras a partir de cualquier dato
- 💰 **Control de precios** (símbolo $/€ configurable, cálculo de valor total del stock)
- 💾 **Persistencia local** (los datos se guardan automáticamente en tu navegador)
- 📤 **Exportación/Importación** de toda la base de datos como JSON

---

## 🖼️ **Vista previa**

<img width="1795" height="1010" alt="image" src="https://github.com/user-attachments/assets/1f1dad9f-d02a-4427-8376-bfaa3d217d17" />


---

## ✨ **Características principales**

| Módulo | Funcionalidad |
|--------|---------------|
| 🔍 **Escáner óptico** | Detecta códigos EAN-13, UPC, CODE128, CODE39 con cámara |
| 📋 **CRUD completo** | Añade, edita, elimina y busca productos |
| 💰 **Gestión de precios** | Asigna precio unitario, moneda configurable ($, €, £, ¥) |
| 📊 **Dashboard** | Valor total del inventario y estadísticas automáticas |
| 🏷️ **Generador de códigos** | Crea códigos de barras profesionales |
| 💾 **Base de datos local** | Almacenamiento automático en `localStorage` |
| 📤 **Exportación** | Respaldos en JSON para migrar o compartir |
| 🖨️ **Impresión directa** | Genera etiquetas imprimibles con precio |
| 📱 **Responsive** | Funciona en móvil, tablet y desktop |

---

## 🚀 **Casos de uso**

| Caso | Descripción |
|------|-------------|
| 📚 **Librería personal** | 50+ libros escaneados con precio y valor total |
| 🏪 **Pequeño comercio** | Inventario completo con precios y stock |
| 🧾 **Facturación** | Base de datos precargada para futura app de facturación |
| 🏭 **Almacén** | Control de entradas, salidas y valor de mercancía |
| 🎮 **Coleccionistas** | Discos, videojuegos, figuras con valoración |

---

## 🛠️ **Tecnologías utilizadas**

| Tecnología | Propósito |
|------------|-----------|
| **HTML5 / CSS3** | Estructura y diseño moderno (Glassmorphism) |
| **JavaScript (ES6+)** | Lógica y manipulación del DOM |
| **JsBarcode** | Generación vectorial de códigos de barras |
| **QuaggaJS** | Escáner de códigos de barras por cámara |
| **html2canvas** | Exportación de códigos a PNG |
| **LocalStorage** | Persistencia de datos en el navegador |

---

## 📁 Estructura del proyecto

stock/

├── index.html          # Aplicación principal (todo-en-uno)
├── README.md           # Este archivo
└── (sin dependencias externas, todo vía CDN)

---
## 🗃️ Estructura de la base de datos
```js
{
  id: "1234567890",
  name: "Cien años de soledad",
  brand: "Gabriel García Márquez",  // o autor/fabricante
  code: "9788437604947",            // ISBN / código de barras
  price: 15.99,                     // Precio unitario (número)
  stock: 5,                         // Cantidad disponible
  category: "libros",               // Para organización
  location: "Estante A2",           // Ubicación física
  dateAdded: "2024-01-15"           // Fecha de registro
}

---

## 👤 Autor
Juan Manuel Orellana
---

## 📄 Licencia
Distribuido bajo licencia MIT. Ver LICENSE para más información.

