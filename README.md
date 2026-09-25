# 💵 Libro de Dólares

Aplicación web para registrar ciclos de cambio de divisas entre dólares (USD) y soles peruanos (PEN) y medir la ganancia o pérdida de cada uno en dólares.

Pensada para quien compra y vende dólares de forma recurrente (en casas de cambio, bancos o plataformas digitales) y quiere saber, con números claros, si realmente está ganando.

## Cómo funciona

Cada operación se registra como un **ciclo** de dos pasos:

1. **Venta:** entregas dólares y recibes soles. El ciclo queda abierto.
2. **Recompra:** con todos los soles del ciclo vuelves a comprar dólares. El ciclo se cierra y se calcula el resultado.

Ejemplo:

| Paso | Operación | Resultado |
|---|---|---|
| Venta | US$10,000 a 3.50 | S/ 35,000 |
| Recompra | S/ 35,000 a 3.40 | US$10,294.12 |
| **Ganancia** | | **+US$294.12 (+2.94%)** |

Todo el resultado se expresa en dólares, la moneda del capital.

## Fórmulas

```
Soles recibidos     = USD vendidos × TC venta
USD recibidos       = Soles recibidos ÷ TC recompra
Ganancia USD        = USD recibidos − USD vendidos − (Costos S/ ÷ TC recompra)
Ganancia %          = Ganancia USD ÷ USD vendidos × 100
TC de equilibrio    = (Soles recibidos − Costos S/) ÷ USD vendidos
Diferencial         = (TC venta − TC recompra) × 100   [céntimos]
```

Si se recompra a un tipo de cambio menor que el de equilibrio, el ciclo gana; si es mayor, pierde.

## Funciones

* **Resumen:** capital actual, ganancia total, tasa de acierto e indicadores por ciclo.
* **Ciclo abierto:** soles disponibles, tipo de cambio de equilibrio y simulación con el tipo de cambio del día.
* **Simulador:** calcula un ciclo hipotético antes de operar.
* **Costos:** comisiones y transferencias en soles, descontados de la ganancia.
* **Historial de ciclos:** venta, recompra, diferencial, duración y costos, con edición y eliminación.
* **Evolución:** gráfico del capital en dólares después de cada ciclo cerrado.
* **Respaldo:** exportación e importación en JSON; exportación CSV compatible con Excel.
* **Sincronización:** datos en la nube por cuenta de Google, con funcionamiento sin conexión.
* **App instalable** en el celular y **modo oscuro** automático.

## Supuestos del modelo

* Los ciclos empiezan vendiendo dólares.
* Los ciclos son completos: la recompra usa todos los soles obtenidos en la venta.
* Solo puede haber un ciclo abierto a la vez.

## Tecnología

* HTML, CSS y JavaScript en un solo archivo, sin dependencias de compilación.
* Firebase Authentication (Google) y Cloud Firestore.
* Compatible con cualquier hosting estático (GitHub Pages, Firebase Hosting, Netlify).

## Archivos

```
index.html               aplicación completa
manifest.webmanifest     configuración de app instalable
icon-192.png             ícono
icon-512.png             ícono
icon-maskable-512.png    ícono adaptable para Android
apple-touch-icon.png     ícono para iPhone
```

## Instalación de tu propia copia

Cada copia necesita su propio proyecto de Firebase; así los datos quedan bajo tu control.

1. Haz un fork de este repositorio o descarga los archivos.
2. Crea un proyecto en la [consola de Firebase](https://console.firebase.google.com).
3. Activa **Authentication** con el proveedor **Google**.
4. Crea una base **Cloud Firestore** en modo producción.
5. Publica estas reglas de seguridad en Firestore:

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

6. En **Configuración del proyecto → Tus apps**, registra una app web y copia su configuración.
7. Reemplaza el bloque `firebaseConfig` en `index.html` con esos valores.
8. Publica los archivos en tu hosting. En GitHub Pages: **Settings → Pages → Deploy from a branch**.
9. Agrega el dominio donde publicaste la app en **Authentication → Settings → Dominios autorizados**.

### Restringir el acceso (recomendado)

Con las reglas anteriores, cualquier persona con cuenta de Google puede usar tu copia y guardar datos en tu proyecto (sin ver los de otros usuarios). Para limitarla a cuentas específicas, agrega esta condición en ambas líneas `allow`:

```
&& request.auth.token.email in ['correo1@gmail.com', 'correo2@gmail.com']
```

## Estructura de datos

```
librodolares_usuarios/{uid}                perfil: capitalInicial, tcReferencia
librodolares_usuarios/{uid}/ciclos/{id}    estado, fechaVenta, usdVendidos, tcVenta, nota,
                                           fechaCompra, tcCompra, costoPEN, notaCompra
```

## Seguridad y privacidad

* La configuración de Firebase incluida en `index.html` es pública por diseño; la protección de los datos depende de las reglas de Firestore.
* Cada usuario solo puede leer y escribir sus propios registros.
* La app no envía datos a ningún servicio distinto de Firebase.

## Aviso

Herramienta de registro y cálculo. No constituye asesoría financiera ni tributaria.
