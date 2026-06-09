<img width="1400" height="851" alt="{D33C9F41-B1FB-4B67-A6BF-029914204D65}" src="https://github.com/user-attachments/assets/c5e0c63d-6b20-4979-bc13-2a6ed937f1c0" />

# 📦 Stock — Sistema de Digitalización y Control de Inventarios

[![Demo en vivo](https://img.shields.io/badge/Demo-Live-brightgreen?style=for-the-badge)](https://coderhouse2025-droid.github.io/Stock/)
[![Sin backend](https://img.shields.io/badge/Backend-Ninguno-lightgrey?style=for-the-badge)](#)
[![QR Scanner](https://img.shields.io/badge/Escáner-QR_en_browser-orange?style=for-the-badge)](#3-html5-qr-code--escáner-qr-con-la-cámara)

> Sistema web de digitalización y control de inventarios con escáner QR mediante la cámara del dispositivo. Permite registrar entradas y salidas de stock, generar etiquetas QR por producto, y exportar el inventario. Sin backend, sin instalación, corre 100% en el navegador.

🔗 **Demo:** https://coderhouse2025-droid.github.io/Stock/

---

## 📋 Índice

- [Descripción](#-descripción)
- [Caso de negocio](#-caso-de-negocio)
- [Decisiones técnicas y su justificación](#-decisiones-técnicas-y-su-justificación)
- [Arquitectura del sistema](#-arquitectura-del-sistema)
- [Pipeline de datos: del inventario físico al sistema digital](#-pipeline-de-datos-del-inventario-físico-al-sistema-digital)
- [¿Por qué este camino y no otro?](#-por-qué-este-camino-y-no-otro)
- [Funcionalidades](#-funcionalidades)
- [Cómo usar](#-cómo-usar)
- [Estructura del proyecto](#-estructura-del-proyecto)
- [Limitaciones conocidas y roadmap](#-limitaciones-conocidas-y-roadmap)

---

## 📋 Descripción

**Stock** es una aplicación web de página única (SPA) para digitalizar y controlar inventarios físicos. El operario escanea el código QR de un producto con la cámara del dispositivo, y el sistema registra la entrada o salida, actualiza el stock disponible en tiempo real, y alerta cuando un artículo cae por debajo del umbral mínimo configurado.

Los productos que no tienen QR pueden etiquetarse desde el propio sistema: se genera un código QR único por producto, listo para imprimir y pegar físicamente.

---

## 💼 Caso de negocio

### El problema que resuelve

Un comercio pequeño o mediano — depósito, ferretería, farmacia, almacén — con inventario físico enfrenta un problema de visibilidad: en cualquier momento del día, la respuesta a "¿cuántas unidades nos quedan de X?" requiere ir físicamente a verificar el estante o consultar una planilla que probablemente está desactualizada.

El control de stock manual en planillas Excel tiene tres fallas estructurales: requiere que alguien la actualice conscientemente tras cada movimiento (lo que raramente ocurre), no genera alertas automáticas de stock bajo, y no hay forma de registrar un movimiento en el momento exacto en que ocurre — lejos de la computadora, en el depósito o en la góndola.

El QR resuelve el problema de fricción del registro: el operario con el celular escanea el producto en el momento en que lo mueve, sin tener que recordarlo para después. **El registro ocurre en el lugar y el momento del movimiento físico**, no horas después frente a una planilla.

### El usuario del sistema

El perfil objetivo es el dueño o encargado de un negocio pequeño con stock físico que:

- Actualmente lleva el inventario en una planilla o directamente de memoria
- Tiene al menos un dispositivo con cámara disponible en el depósito o sala de trabajo
- No tiene soporte técnico interno ni presupuesto para software de gestión en la nube
- Necesita saber en cualquier momento qué productos están por agotarse

### La diferencia con el Facturador

Aunque comparten arquitectura (HTML único, sin backend), los problemas que resuelven son distintos. El Facturador opera en el punto de venta al cliente: registra qué se vendió y genera un comprobante. Stock opera en la cadena logística interna: registra qué se tiene, qué ingresa al depósito y qué se consume o vende sin necesariamente emitir un comprobante. Son complementarios — en un negocio real, el Facturador descontaría del stock que gestiona este sistema.

### Por qué corre 100% en el navegador

La misma lógica que en el Facturador aplica aquí: el negocio no debería depender de conectividad para conocer su propio stock. Un corte de internet no puede significar "no sé cuánto tengo". El navegador garantiza disponibilidad total desde cualquier dispositivo — teléfono en el depósito, tablet en la caja, computadora en la oficina — sin instalar nada.

---

## 🧠 Decisiones técnicas y su justificación

### 1. HTML + JavaScript Vanilla en un único archivo

**¿Por qué un único `index.html`?**

El sistema tiene un flujo operativo simple: escanear QR → identificar producto → registrar movimiento → actualizar stock. No hay routing complejo, no hay estado distribuido entre vistas. Un único archivo resuelve esto directamente y mantiene la portabilidad total: el encargado puede abrir el sistema desde un archivo guardado localmente, sin servidor, sin conexión.

Adicionalmente, un único archivo facilita el deployment en GitHub Pages sin configuración y la apertura directa como `file://` para uso offline completo.

---

### 2. TailwindCSS via CDN — no Bootstrap, no CSS propio

**¿Por qué Tailwind?**

Un sistema de inventario de uso interno prioriza claridad funcional sobre identidad visual. Tailwind permite construir una UI limpia y consistente — tablas legibles, botones con estados claros, alertas de color para stock bajo — directamente en el markup sin escribir CSS propio.

El trade-off del bundle completo (~3MB via CDN) es aceptable para uso interno en red local o con buena conectividad. Comparado con Bootstrap, Tailwind evita el "look Bootstrap" genérico y da más control granular sobre los colores de estado (verde para stock normal, amarillo para warning, rojo para stock bajo).

---

### 3. Html5-QrCode — escáner QR con la cámara

**¿Por qué Html5-QrCode y no QuaggaJS o la BarcodeDetector API nativa?**

| Librería | Formato | Soporte browsers | Tamaño |
|----------|---------|-----------------|--------|
| **Html5-QrCode** | QR + barcodes | Chrome, Firefox, Safari, Edge | ~150KB |
| QuaggaJS | Solo barcodes lineales (EAN, Code128) | Amplio | ~80KB |
| ZXing JS | QR + barcodes | Amplio | ~500KB |
| BarcodeDetector API (nativa) | QR + barcodes | Solo Chrome/Edge | 0KB |

La diferencia clave respecto al Facturador (que usa QuaggaJS) es el **tipo de código**: los libros tienen ISBN con código de barras lineal (EAN-13), pero un sistema de inventario genérico necesita soporte QR. Los códigos QR almacenan más información que un barcode lineal — el ID del producto, su nombre y ubicación pueden codificarse en un solo QR, lo que elimina la necesidad de una tabla de lookup en muchos casos.

Html5-QrCode soporta tanto QR como barcodes lineales, tiene una API más moderna que QuaggaJS, y mantiene compatibilidad con Safari — crítico para usuarios con iPhone o iPad en el depósito.

**Fallback manual obligatorio:** igual que en el Facturador, el escáner requiere HTTPS o localhost. El sistema siempre ofrece ingreso manual de código como alternativa sin fricciones.

---

### 4. QRCode.js — generación de etiquetas QR en el cliente

**¿Por qué generar los QR en el browser?**

Cuando un producto nuevo ingresa al inventario sin etiqueta física, el sistema genera su código QR directamente en el browser para imprimir y pegar. La alternativa sería un endpoint de servidor que reciba el ID del producto y devuelva una imagen QR.

QRCode.js (1.5KB minificado) genera la imagen QR como canvas o SVG directamente en el DOM, sin llamadas de red. El QR generado es inmediatamente imprimible con `window.print()`. Para etiquetar 50 productos nuevos de una sola vez, la generación local sin latencia de red es operativamente más fluida.

---

### 5. LocalStorage + IndexedDB — persistencia de inventario

**¿Por qué dos mecanismos de persistencia y no solo uno?**

El inventario es más crítico que el historial de ventas del Facturador: perder el stock actual por una limpieza de caché es más grave que perder un historial de transacciones. Por eso el sistema usa una estrategia de doble persistencia:

- **LocalStorage**: almacena un snapshot del estado actual del inventario en formato JSON liviano. Lectura rápida en cada carga.
- **IndexedDB**: almacena el historial completo de movimientos (entradas y salidas con timestamp). Soporta volúmenes mayores y permite exportar el historial completo sin limitaciones de tamaño.

La separación tiene una lógica operativa: el estado actual (cuánto hay de cada producto ahora) se necesita en cada consulta — va en LocalStorage para acceso inmediato. El historial de movimientos crece indefinidamente — va en IndexedDB que no tiene límite práctico de almacenamiento.

---

### 6. Chart.js — dashboard de stock en tiempo real

**¿Por qué Chart.js?**

El dashboard muestra visualmente qué productos están en zona crítica (stock bajo el mínimo), qué está en zona de advertencia, y qué tiene stock saludable. Un gráfico de barras por categoría y un indicador circular de salud general del inventario son las dos visualizaciones que le permiten al encargado evaluar la situación de un vistazo.

Chart.js (~60KB) entrega estas visualizaciones con animaciones fluidas y responsive sin necesidad de manipulación manual de SVG. Para el caso de uso específico — un gráfico de barras y un doughnut de estado — es la herramienta justa sin sobredimensionar.

---

## 🏗️ Arquitectura del sistema

```
Operario (dispositivo con cámara)
      │
      ├── Escanear QR del producto
      │       └── Html5-QrCode → ID decodificado → lookup en inventario
      │               ├── Producto encontrado → registrar movimiento (entrada/salida)
      │               └── Producto no encontrado → formulario de alta de producto nuevo
      │
      ├── Ingreso manual de código (fallback sin cámara)
      │       └── Input → lookup en inventario → mismo flujo
      │
      ├── Alta de producto nuevo
      │       └── Formulario → guardar en LocalStorage + IndexedDB
      │               └── Generar QR (QRCode.js) → imprimir etiqueta
      │
      ├── Registro de movimiento
      │       └── Entrada / Salida / Ajuste → actualizar stock → guardar en IndexedDB
      │               └── Si stock < mínimo → alerta visual inmediata
      │
      ├── Dashboard
      │       └── Lectura de LocalStorage → Chart.js → gráficos de estado
      │
      └── Export
              ├── Inventario actual → CSV descargable
              └── Historial de movimientos → CSV descargable
```

**Principio de diseño:** el registro ocurre en el punto físico del movimiento, en el momento en que ocurre. Toda la lógica de actualización es síncrona y local — sin latencia de red, sin dependencia de conectividad.

---

## 🔄 Pipeline de datos: del inventario físico al sistema digital

La carga inicial del inventario es el proceso más crítico y el más propenso a errores. Se documenta cada transformación aplicada.

### El "dataset" inicial: inventario físico en estado real

El inventario de un comercio pequeño suele existir en uno de estos formatos cuando se decide digitalizarlo:
- Una planilla Excel con columnas inconsistentes y sin normalizar
- Un CSV exportado de algún sistema anterior (Tango, un Excel viejo, un sistema contable)
- En el peor caso: solo existe en la cabeza del dueño y hay que construirlo desde cero

El sistema acepta los tres casos: importación de CSV, carga manual producto por producto, o una combinación de ambos.

---

#### Problema 1: Códigos de producto inconsistentes

Los inventarios históricos tienen códigos de producto generados por distintos criterios a lo largo del tiempo: algunos son numéricos (`00123`), algunos alfanuméricos (`FERR-001`), algunos tienen ceros a la izquierda significativos (`001` ≠ `1`), algunos tienen espacios accidentales (`PROD 001 `).

Un código inconsistente rompe el lookup al escanear: el QR codifica `FERR-001` pero el sistema busca `ferr-001` y no lo encuentra.

**Transformación aplicada — normalización de código al cargar y al buscar:**

```javascript
function normalizarCodigo(codigo) {
  return codigo
    .toString()
    .trim()                    // eliminar espacios al inicio/fin
    .toUpperCase()             // unificar mayúsculas
    .replace(/\s+/g, '-')     // espacios internos → guiones
    .replace(/[^A-Z0-9\-_]/g, ''); // eliminar caracteres especiales
}
// "ferr 001 " → "FERR-001"
// " PROD_001" → "PROD_001"
```

La misma función se aplica tanto al guardar el producto como al decodificar el QR escaneado, garantizando que la comparación siempre opere sobre la forma normalizada.

---

#### Problema 2: Stock inicial negativo o nulo

Al importar un inventario histórico, algunos productos pueden tener stock 0 o incluso valores negativos (producto que fue vendido más de lo registrado como entrada). Stock negativo es técnicamente inválido pero operativamente posible — refleja una inconsistencia histórica en el registro.

**Transformación aplicada:**

```javascript
function parsearStock(valor) {
  const num = parseInt(valor, 10);
  if (isNaN(num)) return 0;       // valor no numérico → 0
  return Math.max(0, num);        // negativo → 0, con flag de advertencia
}
```

Los productos con stock inicial negativo en el CSV se cargan con stock 0 y se marcan con un flag `inconsistencia_historica: true`. El dashboard los muestra en un panel separado para que el encargado los revise y corrija manualmente.

---

#### Problema 3: Umbrales mínimos ausentes

El sistema emite alertas cuando el stock cae por debajo de un umbral mínimo configurado por producto. En el CSV de importación, este campo frecuentemente no existe o está vacío.

**Transformación aplicada — inferencia de umbral por defecto:**

```javascript
function umbralMinimoPorDefecto(stockInicial) {
  if (stockInicial <= 0) return 1;
  if (stockInicial <= 10) return Math.ceil(stockInicial * 0.2); // 20% del stock inicial
  if (stockInicial <= 100) return Math.ceil(stockInicial * 0.1); // 10%
  return Math.ceil(stockInicial * 0.05); // 5% para productos de alto volumen
}
```

Esta heurística es una aproximación razonable para el arranque: un producto con 50 unidades iniciales tendrá umbral de alerta en 5. El encargado puede ajustar cada umbral manualmente una vez el sistema esté en uso y conozca mejor sus patrones de reposición.

---

#### Problema 4: Nombres de producto duplicados con variantes

Un inventario histórico acumula el mismo producto con distintas grafías: `Tornillo 6mm`, `tornillo 6 mm`, `TORNILLO 6MM`, `Tornillos 6mm` (plural). Son cuatro registros para lo que operativamente es el mismo producto.

**Detección al importar:**

```javascript
function detectarPosiblesDuplicados(productos) {
  const normalizados = productos.map(p => ({
    ...p,
    _nombreNorm: p.nombre
      .toLowerCase()
      .replace(/\s+/g, ' ')
      .trim()
      .replace(/s\b/g, '')  // singular/plural básico
  }));

  const grupos = _.groupBy(normalizados, '_nombreNorm');
  return Object.values(grupos).filter(g => g.length > 1);
}
```

El sistema no fusiona automáticamente los duplicados — esa decisión requiere criterio humano. En cambio, al finalizar la importación muestra un panel de "posibles duplicados detectados" con los grupos de productos similares para que el encargado decida si unificarlos o mantenerlos separados (pueden ser variantes legítimas de un mismo producto base).

---

#### Problema 5: Categorías libres sin jerarquía

Los inventarios históricos tienen categorías asignadas sin criterio: `Herramientas`, `herramienta`, `Herr.`, `Herramientas manuales` pueden ser la misma categoría. Sin normalización, el dashboard muestra 4 barras para lo que debería ser 1.

**Transformación aplicada:**

```javascript
const categoriasNormalizadas = new Map();

function normalizarCategoria(cat) {
  if (!cat) return 'Sin categoría';
  const key = cat.toLowerCase().trim().replace(/[^a-záéíóúñ\s]/g, '');
  if (!categoriasNormalizadas.has(key)) {
    categoriasNormalizadas.set(key, cat.trim()); // guardar forma canónica
  }
  return categoriasNormalizadas.get(key);
}
```

Al igual que con los duplicados de productos, el sistema presenta al usuario las categorías similares detectadas para que las unifique manualmente si lo considera necesario.

---

#### Problema 6: Timestamps de movimientos sin zona horaria

El historial de movimientos almacena cuándo ocurrió cada entrada o salida. Si los timestamps se guardan sin zona horaria, los reportes pueden mostrar movimientos en horarios incorrectos según el dispositivo desde el que se consulta.

**Transformación aplicada:**

```javascript
// Guardar siempre en ISO 8601 con offset de zona horaria local
function timestampActual() {
  return new Date().toISOString(); // UTC — consistente en todos los dispositivos
}

// Al mostrar al usuario, convertir a hora local del browser
function formatearFechaLocal(isoString) {
  return new Date(isoString).toLocaleString('es-AR', {
    timeZone: Intl.DateTimeFormat().resolvedOptions().timeZone
  });
}
```

Se almacena en UTC (agnóstico a zona horaria) y se muestra en hora local al renderizar. Esto garantiza que el historial sea consistente aunque el encargado consulte el sistema desde dispositivos con distintas configuraciones de zona horaria.

---

## 🤔 ¿Por qué este camino y no otro?

### Alternativa descartada: sistema con backend + base de datos central

Un backend con PostgreSQL y una API REST daría: sincronización en tiempo real entre múltiples dispositivos, historial centralizado, backups automáticos, y acceso desde cualquier dispositivo con internet. El costo: infraestructura, costos mensuales, y dependencia de conectividad para registrar un movimiento.

Para el usuario objetivo, la operación en el depósito no siempre tiene buena señal. La capacidad de registrar movimientos offline y sincronizar después es más valiosa que la sincronización en tiempo real permanente. El roadmap contempla esta evolución con una estrategia offline-first + sync, no como un cambio de arquitectura total.

### Alternativa descartada: app nativa (React Native / Flutter)

Una app nativa daría acceso a APIs del sistema operativo más maduras para el escáner y la impresión de etiquetas. El trade-off: proceso de publicación en tiendas, instalación obligatoria, y actualizaciones que el usuario debe aceptar. Una PWA con Html5-QrCode resuelve el 90% del caso de uso sin esas fricciones.

### Alternativa descartada: Google Sheets como base de datos

Usar la API de Google Sheets como backend de datos es un patrón común para proyectos pequeños. El problema para este caso: requiere autenticación de Google (fricción de setup), tiene límites de rate en la API de escritura que pueden generar errores bajo uso intenso, y agrega latencia en cada registro de movimiento. LocalStorage + IndexedDB es más rápido y más confiable para operación local.

### Por qué QR y no código de barras lineal

El Facturador usa código de barras lineal (EAN-13 / ISBN) porque los libros ya vienen con ese código impreso de fábrica. Un sistema de inventario genérico no puede asumir que los productos tienen código de barras preexistente — en muchos comercios, los productos son artículos propios sin código estándar. El QR permite codificar información arbitraria (código interno, nombre, ubicación) en una etiqueta que el propio sistema genera e imprime. Esta capacidad de autoetiquetado es estructural al flujo de trabajo.

---

## ✨ Funcionalidades

- 📷 **Escáner QR** vía cámara del dispositivo (Html5-QrCode, requiere HTTPS o localhost)
- ✏️ **Ingreso manual** de código como fallback al escáner
- ➕ **Alta de productos** con código, nombre, categoría, stock inicial y umbral mínimo
- 🏷️ **Generación de etiquetas QR** para imprimir y pegar en productos sin código
- 📥 **Registro de entradas** (reposición, compra, devolución de cliente)
- 📤 **Registro de salidas** (venta, consumo interno, merma)
- ⚠️ **Alertas de stock bajo** cuando el nivel cae por debajo del umbral configurado
- 📊 **Dashboard visual** con estado del inventario por categoría (Chart.js)
- 📋 **Historial de movimientos** con timestamp, tipo y cantidad
- 📥 **Export a CSV** del inventario actual e historial de movimientos
- 📦 **Importación masiva** desde CSV con normalización automática
- 📱 **Responsive** — funciona en celular, tablet y desktop

---

## 🚀 Cómo usar

### Primera vez: cargar el inventario

**Opción A — Importar CSV existente:**

El CSV debe tener columnas de código, nombre, stock y categoría (los nombres exactos se detectan automáticamente):

```csv
codigo,nombre,categoria,stock,stock_minimo
FERR-001,Tornillo 6mm cabeza plana,Ferretería,200,20
FERR-002,Tuerca 6mm,Ferretería,150,15
ELEC-001,Cable 2.5mm x metro,Electricidad,500,50
```

**Opción B — Carga manual:**
Ir a "Nuevo producto" → completar el formulario → el sistema genera automáticamente el QR para imprimir.

### Operación diaria

1. Escanear el QR del producto con la cámara (o ingresar el código manualmente)
2. Seleccionar el tipo de movimiento: Entrada / Salida / Ajuste
3. Ingresar la cantidad
4. Confirmar — el stock se actualiza inmediatamente

### Ver el estado del inventario

El dashboard muestra en tiempo real:
- Productos en stock crítico (rojo) — por debajo del mínimo
- Productos en advertencia (amarillo) — entre 100% y 150% del mínimo
- Productos con stock saludable (verde)

---

## 📁 Estructura del proyecto

```
/
├── index.html     # Aplicación completa: markup + estilos (Tailwind) + lógica JS
│                  # Dependencias cargadas desde CDN: Html5-QrCode, QRCode.js,
│                  # Chart.js, PapaParse
└── README.md
```

**¿Por qué todo en un único archivo?**

La misma razón que en el Facturador: portabilidad total. El encargado del depósito puede abrir el sistema directamente desde un archivo guardado localmente, sin instalar Node.js, sin servidor local, sin ningún paso técnico adicional. Esa accesibilidad es parte del valor del sistema para su usuario objetivo.

---

## ⚠️ Limitaciones conocidas y roadmap

| Limitación | Impacto | Solución futura |
|------------|---------|----------------|
| **Sin multi-dispositivo** | Cada dispositivo tiene su propio inventario local | Sincronización via Firebase / Supabase |
| **LocalStorage borrable** | Riesgo de pérdida de datos | Export CSV periódico; futura nube |
| **Sin usuarios/roles** | Cualquiera con acceso puede modificar el inventario | Autenticación básica con PIN por dispositivo |
| **Impresión de etiquetas básica** | Solo impresión directa desde browser | Generación de PDF con múltiples etiquetas para impresora de etiquetas |
| **Escáner requiere HTTPS** | No funciona en `file://` ni HTTP plano | Usar ingreso manual como fallback (ya implementado) |
| **Sin integración con Facturador** | Los movimientos de venta no descuentan stock automáticamente | Integración entre ambos sistemas como módulo unificado |

---

## 👨‍💻 Autor

**Juan Manuel Orellana**

---

## 📄 Licencia

MIT License — libre para uso, adaptación y distribución.
