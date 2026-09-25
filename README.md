# 💵 Libro de Dólares

Registro personal de ciclos de cambio USD/PEN. Cada ciclo es una venta de dólares y su recompra posterior; la app calcula cuántos dólares ganaste o perdiste en cada uno.

**App:** https://mauricio5521.github.io/Libro-dolares/

## Cómo funciona

Un ciclo tiene dos pasos:

1. **Venta:** entregas dólares y recibes soles. El ciclo queda abierto.
2. **Recompra:** con todos los soles del ciclo vuelves a comprar dólares. El ciclo se cierra y se calcula la ganancia.

Ejemplo:

| Paso | Operación | Resultado |
|---|---|---|
| Venta | US$10,000 a 3.50 | S/ 35,000 |
| Recompra | S/ 35,000 a 3.40 | US$10,294.12 |
| **Ganancia** | | **+US$294.12 (+2.94%)** |

Toda la ganancia se mide en dólares, la moneda del capital.

## Fórmulas

```
Soles recibidos     = USD vendidos × TC venta
USD recibidos       = Soles recibidos ÷ TC recompra
Ganancia USD        = USD recibidos − USD vendidos − (Costos S/ ÷ TC recompra)
Ganancia %          = Ganancia USD ÷ USD vendidos × 100
TC de equilibrio    = (Soles recibidos − Costos S/) ÷ USD vendidos
Diferencial         = (TC venta − TC recompra) × 100   [céntimos]
```

Si recompras a un TC menor que el de equilibrio, ganas. Si es mayor, pierdes.

## Funciones

* **Resumen:** capital actual, ganancia total, tasa de acierto e indicadores por ciclo.
* **Ciclo abierto:** soles en mano, TC de equilibrio y simulación con el TC del día (resultado no realizado).
* **Simulador:** calcula un ciclo hipotético antes de operar.
* **Costos:** comisiones y transferencias en soles, descontados de la ganancia.
* **Ciclos:** historial con venta, recompra, diferencial, duración y costos. Edición y eliminación.
* **Evolución:** gráfico del capital en dólares después de cada ciclo cerrado.
* **Respaldo:** exportación e importación JSON; exportación CSV para Excel.
* **Sincronización:** datos en la nube por cuenta de Google, con soporte sin conexión.

## Supuestos del modelo

* Los ciclos siempre empiezan vendiendo dólares.
* Los ciclos son completos: la recompra usa todos los soles de la venta.
* Solo puede haber un ciclo abierto a la vez.

## Tecnología

* HTML, CSS y JavaScript en un solo archivo (`index.html`), sin dependencias de compilación.
* Firebase Authentication (Google) y Cloud Firestore.
* Publicado con GitHub Pages.

## Estructura de datos

```
librodolares_usuarios/{uid}                perfil: capitalInicial, tcReferencia, migracionCiclos
librodolares_usuarios/{uid}/ciclos/{id}    estado, fechaVenta, usdVendidos, tcVenta, nota,
                                           fechaCompra, tcCompra, costoPEN, notaCompra
librodolares_usuarios/{uid}/ops/{id}       formato anterior (solo lectura, para migración)
```

## Configuración

1. Crear un proyecto en [Firebase](https://console.firebase.google.com).
2. Activar **Authentication** con el proveedor Google.
3. Crear la base **Cloud Firestore** en modo producción.
4. Publicar estas reglas en Firestore:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /librodolares_usuarios/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
      match /{sub}/{docId} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
    }
  }
}
```

5. Reemplazar el bloque `firebaseConfig` de `index.html` con las credenciales del proyecto.
6. Agregar el dominio de publicación en **Authentication → Settings → Dominios autorizados**.

La configuración de Firebase en `index.html` es pública por diseño; la protección de los datos depende de las reglas de Firestore.

## Aviso

Herramienta de registro personal. No constituye asesoría financiera.
