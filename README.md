<p align="center">
  <img src="public/images/HatoManager-logo.png" width="130" alt="Logo de HatoManager">
</p>

<h1 align="center">HatoManager</h1>

<p align="center">
  <strong>Gestión ganadera inteligente y comercialización de ganado en una sola plataforma.</strong><br>
  Inventario del hato, sanidad, pesajes, fincas, veterinarios y una tienda en línea con pagos a través de Wompi.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Laravel-12-FF2D20?logo=laravel&logoColor=white" alt="Laravel 12">
  <img src="https://img.shields.io/badge/PHP-8.2%2B-777BB4?logo=php&logoColor=white" alt="PHP 8.2+">
  <img src="https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black" alt="React 18">
  <img src="https://img.shields.io/badge/Inertia.js-2-9553E9?logo=inertia&logoColor=white" alt="Inertia 2">
  <img src="https://img.shields.io/badge/Tailwind_CSS-3-06B6D4?logo=tailwindcss&logoColor=white" alt="Tailwind CSS">
  <img src="https://img.shields.io/badge/MySQL-8-4479A1?logo=mysql&logoColor=white" alt="MySQL">
  <img src="https://img.shields.io/badge/Pagos-Wompi-2C2A29" alt="Wompi">
</p>

<p align="center">
  <img src="docs/screenshots/08-dashboard.png" alt="Dashboard del ganadero" width="100%">
</p>

---

## Tabla de contenido

- [Descripción](#descripción)
- [Características](#características)
- [Roles y permisos](#roles-y-permisos)
- [Capturas de pantalla](#capturas-de-pantalla)
- [Stack tecnológico](#stack-tecnológico)
- [Arquitectura del proyecto](#arquitectura-del-proyecto)
- [Modelo de datos](#modelo-de-datos)
- [Flujo de compra y estados del pedido](#flujo-de-compra-y-estados-del-pedido)
- [Tareas programadas y notificaciones](#tareas-programadas-y-notificaciones)
- [Instalación](#instalación)
- [Variables de entorno](#variables-de-entorno)
- [Usuarios de prueba](#usuarios-de-prueba)
- [Comandos útiles](#comandos-útiles)
- [Despliegue con Docker](#despliegue-con-docker)
- [Rutas principales](#rutas-principales)
- [Pruebas](#pruebas)
- [Autores](#autores)

---

## Descripción

**HatoManager** es una aplicación web para ganaderos colombianos que reúne en un mismo lugar la **administración técnica del hato** y la **venta directa de animales** a compradores.

- El **ganadero** administra una o varias fincas, sus lotes/potreros y el inventario de animales. También registra pesajes y eventos sanitarios, invita veterinarios y publica animales para la venta.
- El **veterinario** recibe acceso a las fincas que le asignan y lleva el control sanitario y de peso de los animales.
- El **comprador** explora el catálogo público, agrega animales al carrito y paga en línea con **Wompi**. Después hace seguimiento de sus pedidos.
- El **administrador** gestiona usuarios, roles, permisos y los catálogos maestros de la plataforma (razas, categorías, tipos de pasto, etapas productivas y métodos de pesaje).

Toda la información del panel se filtra por la **finca activa**, que se elige al iniciar sesión y se puede cambiar desde la barra superior.

---

## Características

### 🐄 Gestión del hato
- **Inventario de animales** con foto (Spatie Media Library), arete único por finca, raza, categoría, lote, sexo, fecha de nacimiento, precio, peso objetivo y precio por kilo.
- Filtros por nombre/arete, estado, raza, categoría y rango de nacimiento. Eliminación lógica (*soft delete*) con restauración.
- **Ficha del animal** con la curva de evolución de peso (Recharts), el historial de pesajes y sanitario, y la **proyección de venta**: ganancia diaria de peso (GDP), días estimados para llegar al peso objetivo, fecha estimada y utilidad esperada.
- **Búsqueda global** de animales por nombre, arete o lote desde la barra superior.
- Estados del animal: `Activo`, `Inactivo`, `Publicado`, `Reservado`, `Vendido` y `Muerto` (este último con el motivo de la muerte).

### 🏡 Fincas y lotes
- Varias fincas por ganadero, con área, ubicación (ciudad/departamento), peso objetivo y precio por kilo de referencia.
- Selección de la **finca activa** al iniciar sesión. No se puede desactivar una finca que todavía tenga animales o pedidos.
- **Lotes/potreros** con área, tipo de pasto, capacidad y número de animales asignados.

### 💉 Sanidad
- Registro de **vacunas, desparasitaciones y tratamientos** con producto, dosis, fecha de aplicación, próxima fecha y notas.
- **Alertas sanitarias** automáticas (vencidas y próximas a 7 días), visibles en el dashboard y en el módulo de sanidad.
- **Certificados sanitarios en PDF** (DomPDF), individuales o por lote de la finca, en versión a color o en blanco y negro, con las firmas del ganadero y del veterinario.

### ⚖️ Pesajes
- Registro de peso con condición corporal (BCS 1–5), etapa productiva, método de pesaje, ayuno previo y temperatura ambiente.

### 📊 Dashboard
- KPIs del hato por estado (activos, publicados, reservados, vendidos, inactivos, muertos).
- Hato total y peso promedio, vacunas al día, ingresos por ventas y órdenes pendientes.
- Gráficas de evolución mensual del hato (ingresos vs. salidas), peso promedio histórico, ventas mensuales y distribución por estado y por categoría.
- Tabla paginada de alertas sanitarias vencidas y próximas.

### 🩺 Veterinarios
- El ganadero **invita veterinarios por correo** a una finca concreta.
  - Si el veterinario ya tiene cuenta, recibe un correo para **aceptar o rechazar** la invitación.
  - Si no la tiene, recibe un enlace con **token válido por 48 horas** para registrarse, y queda vinculado automáticamente.
- El ganadero puede cancelar invitaciones pendientes y desvincular veterinarios (el veterinario recibe un aviso por correo).

### 🛒 Tienda en línea (e-commerce)
- Página de inicio pública, **catálogo de animales en venta** con filtros y paginación, y ficha pública con pestañas de descripción, historial de peso y sanidad.
- **Carrito de compras** agrupado por finca, que guarda el precio al momento de agregar el animal.
- **Checkout con Wompi**: firma de integridad SHA-256, verificación del resultado contra la API de Wompi y un **webhook** con validación de firma.
- **Reembolsos automáticos** (void en Wompi) cuando el ganadero rechaza un animal ya pagado.
- Historial de pedidos del comprador, con la opción de cancelar los que estén pendientes de pago.
- Panel de **pedidos recibidos** para que el ganadero confirme o rechace cada animal.
- Páginas informativas: Sobre nosotros, Contacto (con una bandeja de mensajes en el panel del administrador), Ayuda, Términos de uso y Privacidad.

### 🔐 Seguridad y cuentas
- Autenticación con **Laravel Fortify**: registro, verificación de correo, recuperación de contraseña y confirmación de contraseña.
- **Autenticación de dos factores (2FA)** con código QR, confirmación y códigos de recuperación.
- **Roles y permisos** con Spatie Laravel Permission. El menú lateral se arma según los permisos del usuario.
- *Rate limiting* en el login y en el 2FA (5 intentos por minuto).
- Redirección según el rol al iniciar sesión.

---

## Roles y permisos

| Rol | Permisos | A dónde llega al iniciar sesión |
|---|---|---|
| **admin** | gestionar usuarios, roles, permisos, mensajes, razas, categorías de animales, etapas productivas y métodos de pesaje | `/users` |
| **ganadero** | gestionar dashboard, animales, lotes, sanidad, fincas, pesos, veterinarios, ventas y subastas | `/select-farm` → `/dashboard` |
| **veterinario** | gestionar sanidad, animales y pesos (solo en las fincas asignadas) | `/select-farm` → `/animals` |
| **comprador** | gestionar e-commerce | `/` (tienda) |

> Los roles y permisos se crean con `RolesAndPermissionsSeeder` y el administrador puede editarlos desde el panel.

---

## Capturas de pantalla

> Las capturas se tomaron con los datos de prueba de los *seeders*.

### Tienda pública

| Inicio | Catálogo de ventas |
|---|---|
| ![Inicio](docs/screenshots/01-inicio.png) | ![Catálogo](docs/screenshots/02-catalogo.png) |

| Detalle del animal en venta | Contacto |
|---|---|
| ![Detalle de venta](docs/screenshots/03-detalle-animal-venta.png) | ![Contacto](docs/screenshots/04-contacto.png) |

### Autenticación

| Iniciar sesión | Registro | Selección de finca activa |
|---|---|---|
| ![Login](docs/screenshots/05-login.png) | ![Registro](docs/screenshots/06-registro.png) | ![Seleccionar finca](docs/screenshots/07-seleccionar-finca.png) |

### Panel del ganadero

**Dashboard**

![Dashboard](docs/screenshots/08-dashboard.png)

| Inventario del hato | Registrar animal |
|---|---|
| ![Animales](docs/screenshots/09-animales.png) | ![Registrar animal](docs/screenshots/11-registrar-animal.png) |

| Ficha del animal | Evolución de peso e historial |
|---|---|
| ![Ficha del animal](docs/screenshots/10-ficha-animal.png) | ![Evolución de peso](docs/screenshots/10b-ficha-animal-peso.png) |

| Sanidad animal | Historial de pesajes |
|---|---|
| ![Sanidad](docs/screenshots/12-sanidad.png) | ![Pesajes](docs/screenshots/13-pesajes.png) |

| Lotes | Mis fincas |
|---|---|
| ![Lotes](docs/screenshots/14-lotes.png) | ![Mis fincas](docs/screenshots/15-mis-fincas.png) |

| Mis veterinarios | Perfil y verificación en dos pasos |
|---|---|
| ![Veterinarios](docs/screenshots/16-mis-veterinarios.png) | ![Perfil 2FA](docs/screenshots/19-perfil-2fa.png) |

**Ventas y pedidos**

| Mis transacciones | Pedidos recibidos (confirmar/rechazar) |
|---|---|
| ![Transacciones](docs/screenshots/17-ventas-ganadero.png) | ![Pedidos recibidos](docs/screenshots/18-pedidos-recibidos.png) |

**Certificado sanitario en PDF**

<p align="center">
  <img src="docs/screenshots/20-certificado-sanitario.png" alt="Certificado sanitario individual" width="60%">
</p>

### Comprador

| Carrito de compras | Mis pedidos |
|---|---|
| ![Carrito](docs/screenshots/25-carrito.png) | ![Mis pedidos](docs/screenshots/26-mis-pedidos.png) |

### Administración

| Usuarios | Roles |
|---|---|
| ![Usuarios](docs/screenshots/21-admin-usuarios.png) | ![Roles](docs/screenshots/22-admin-roles.png) |

| Permisos | Razas |
|---|---|
| ![Permisos](docs/screenshots/23-admin-permisos.png) | ![Razas](docs/screenshots/24-admin-razas.png) |

---

## Stack tecnológico

| Capa | Tecnologías |
|---|---|
| **Backend** | PHP 8.2+, Laravel 12, Laravel Fortify (autenticación y 2FA), Laravel Sanctum, Laravel Reverb |
| **Frontend** | React 18, Inertia.js 2, Vite 7, Tailwind CSS 3, Headless UI, React Icons, Recharts |
| **Paquetes** | Spatie Laravel Permission (roles/permisos), Spatie Media Library (imágenes), Barryvdh DomPDF (certificados), Ziggy (rutas en JS), Laravel Lang (traducción al español) |
| **Base de datos** | MySQL / MariaDB |
| **Colas y caché** | Driver `database` (jobs, caché y sesiones en base de datos) |
| **Pagos** | Wompi (sandbox / producción) |
| **Correo** | SMTP (Gmail u otro proveedor) |
| **Calidad de código** | ESLint, Prettier (con plugins de Tailwind y orden de imports), Laravel Pint, PHPUnit |

---

## Arquitectura del proyecto

```text
HatoManager/
├── app/
│   ├── Actions/Fortify/        # Registro, reseteo y actualización de usuario (Fortify)
│   ├── Http/
│   │   ├── Controllers/        # 30+ controladores (Animal, Farm, Paddock, HealthRecord, Checkout, ...)
│   │   ├── Middleware/         # HandleInertiaRequests (props compartidas: auth, finca activa, notificaciones)
│   │   ├── Requests/           # Validaciones (AnimalRequest, HealthRecordRequest, WeightRecordRequest, ...)
│   │   └── Responses/          # Redirecciones por rol después del login / 2FA / verificación de correo
│   ├── Jobs/                   # CancelarPedidosPendientes, CheckVacunasVencidas
│   ├── Mail/                   # AlertasSanitariasMail
│   ├── Models/                 # 19 modelos Eloquent
│   ├── Notifications/          # Notificaciones de pedidos, sanidad e invitaciones
│   ├── Providers/              # AppServiceProvider, FortifyServiceProvider
│   └── Services/WompiService.php
├── database/
│   ├── migrations/             # Esquema completo (fincas, animales, sanidad, pesajes, carrito, pedidos, ...)
│   └── seeders/                # Datos de prueba realistas (20 fincas, ~470 animales, pedidos, ...)
├── resources/
│   ├── js/
│   │   ├── Pages/              # Vistas Inertia (Animales, Sanidad, HistorialPesos, EcommerceVentas, ...)
│   │   ├── Components/         # Componentes agrupados por módulo
│   │   ├── Layouts/            # AuthenticatedLayout (panel) y EcommerceLayout (tienda)
│   │   ├── hooks/useRole.jsx   # Helper de roles/permisos en el frontend
│   │   └── helpers/            # Formato de dinero y fechas
│   └── views/
│       ├── emails/             # Plantillas de correo (invitaciones, alertas, verificación, ...)
│       └── pdf/                # Certificados sanitarios (individual / lote, color / B&N)
├── routes/
│   ├── web.php                 # Rutas agrupadas por rol
│   ├── auth.php
│   └── console.php             # Programación de jobs (scheduler)
├── docs/screenshots/           # Capturas usadas en este README
└── Dockerfile
```

---

## Modelo de datos

```mermaid
erDiagram
    USERS }o--o{ FARMS : "farm_user"
    FARMS ||--o{ PADDOCKS : tiene
    FARMS ||--o{ ANIMALS : tiene
    FARMS ||--o{ VETERINARIAN_INVITATIONS : genera
    TYPE_GRASSES ||--o{ PADDOCKS : clasifica
    PADDOCKS ||--o{ ANIMALS : aloja
    BREEDS ||--o{ ANIMALS : clasifica
    ANIMAL_CATEGORIES ||--o{ ANIMALS : clasifica
    ANIMALS ||--o{ HEALTH_RECORDS : registra
    HEALTH_RECORDS ||--o{ HEALTH_ALERTS : genera
    ANIMALS ||--o{ WEIGHT_RECORDS : registra
    PRODUCTIVE_STAGES ||--o{ WEIGHT_RECORDS : clasifica
    WEIGHT_METHODS ||--o{ WEIGHT_RECORDS : clasifica
    USERS ||--|| CARTS : posee
    CARTS ||--o{ CART_ITEMS : contiene
    ANIMALS ||--o{ CART_ITEMS : referencia
    USERS ||--o{ ORDERS : realiza
    ORDERS }o--o{ ANIMALS : "animal_order"
    TRANSACTIONS ||--o| ORDERS : paga
    TRANSACTIONS ||--o{ TRANSACTIONS : "reembolso de"
```

**Tablas de soporte:** `roles`, `permissions` y pivotes (Spatie), `media` (Media Library), `notifications`, `jobs`, `cache`, `sessions`, `contacts`, `passkeys`.

---

## Flujo de compra y estados del pedido

```mermaid
stateDiagram-v2
    [*] --> PendienteDePago: Checkout (animales pasan a "Reservado")
    PendienteDePago --> PendienteDeConfirmacion: Wompi aprueba el pago
    PendienteDePago --> CanceladoPorComprador: El comprador cancela
    PendienteDePago --> CanceladoPorExpiracion: Pasan 24 h sin pago (job horario)
    PendienteDeConfirmacion --> Completado: El ganadero confirma (animal "Vendido")
    PendienteDeConfirmacion --> RechazadoPorGanadero: El ganadero rechaza todo (reembolso en Wompi)
    Completado --> [*]
    RechazadoPorGanadero --> [*]
    CanceladoPorComprador --> [*]
    CanceladoPorExpiracion --> [*]
```

1. El comprador agrega animales `Publicado` al carrito. Se guarda el precio de ese momento.
2. En `/checkout` se crea el pedido con la referencia `HM-XXXXXXXXXX`, los animales quedan `Reservado` y se genera la firma de integridad para el widget de Wompi.
3. Wompi redirige a `/checkout/resultado` y además envía el webhook a `/webhook/wompi`. Ambos caminos verifican el pago y se protegen contra el procesamiento duplicado.
4. Se notifica a cada ganadero, que **confirma o rechaza cada animal** por separado. Si rechaza, se pide automáticamente el reembolso del valor de ese animal en Wompi y el animal vuelve a `Publicado`.

---

## Tareas programadas y notificaciones

**Scheduler** (`routes/console.php`):

| Job | Frecuencia | Qué hace |
|---|---|---|
| `CancelarPedidosPendientes` | Cada hora | Cancela los pedidos con más de 24 h en "Pendiente de pago", libera los animales reservados y avisa al comprador |
| `CheckVacunasVencidas` | Todos los días a las 07:00 | Envía a cada ganadero un correo y una notificación con las alertas sanitarias vencidas o próximas, agrupadas por finca |

**Notificaciones:**

| Notificación | Canal | Destinatario |
|---|---|---|
| `PedidoRecibidoNotification` | Base de datos (campana) | Ganadero |
| `PedidoCanceladoNotification` | Base de datos | Ganadero |
| `AnimalConfirmadoNotification` / `AnimalRechazadoNotification` | Base de datos | Comprador |
| `PedidoExpiradoNotification` | Base de datos | Comprador |
| `VacunasProximasNotification` + `AlertasSanitariasMail` | Base de datos + correo | Ganadero |
| `VeterinarianInvitationNewUser` / `VeterinarianInvitationExistingUser` | Correo | Veterinario invitado |
| `VeterinarianUnlinkedFromFarm` | Correo | Veterinario |

---

## Instalación

### Requisitos

- PHP **8.2 o superior** con las extensiones `pdo_mysql`, `mbstring`, `gd`, `bcmath`, `exif` y `fileinfo`
- Composer 2
- Node.js **20 o superior** y npm
- MySQL 8 o MariaDB 10.4 o superior (el dashboard usa funciones propias de MySQL como `DATE_FORMAT`, así que **SQLite no sirve**)
- Conexión a internet para ejecutar los *seeders* (las fotos de los animales se descargan de URLs públicas)

### Pasos

```bash
# 1. Clonar el repositorio
git clone https://github.com/Daniel-RN24/HatoManager
cd HatoManager

# 2. Instalar dependencias
composer install
npm install

# 3. Configurar el entorno
cp .env.example .env
php artisan key:generate
# Edita .env: base de datos MySQL, correo y llaves de Wompi (ver la sección siguiente)

# 4. Crear la base de datos (por ejemplo "hatomanager") y ejecutar migraciones + seeders
php artisan migrate --seed

# 5. Enlazar el almacenamiento público (fotos de los animales)
php artisan storage:link

# 6. Levantar el entorno de desarrollo (servidor + cola + logs + Vite)
composer dev
```

La aplicación queda disponible en **http://localhost:8000**.

> Para que corran los jobs programados (expiración de pedidos y alertas sanitarias), ejecuta en otra terminal:
> ```bash
> php artisan schedule:work
> ```

---

## Variables de entorno

Además de las variables estándar de Laravel, HatoManager necesita estas:

```dotenv
APP_NAME=HatoManager
APP_URL=http://localhost:8000
APP_LOCALE=es
APP_FALLBACK_LOCALE=es

# Base de datos (MySQL obligatorio)
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=hatomanager
DB_USERNAME=root
DB_PASSWORD=

QUEUE_CONNECTION=database
SESSION_DRIVER=database

# Correo (verificación, recuperación de contraseña, invitaciones y alertas)
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=tu-correo@gmail.com
MAIL_PASSWORD=tu-contraseña-de-aplicación
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=tu-correo@gmail.com
MAIL_FROM_NAME="${APP_NAME}"

# Pagos con Wompi (https://comercios.wompi.co)
WOMPI_PUBLIC_KEY=pub_test_xxxxxxxx
WOMPI_PRIVATE_KEY=prv_test_xxxxxxxx
WOMPI_EVENTS_SECRET=test_events_xxxxxxxx
WOMPI_INTEGRITY_SECRET=test_integrity_xxxxxxxx
WOMPI_SANDBOX=true

# Broadcasting (opcional, Laravel Reverb)
BROADCAST_CONNECTION=reverb
REVERB_APP_ID=
REVERB_APP_KEY=
REVERB_APP_SECRET=
REVERB_HOST=localhost
REVERB_PORT=8080
REVERB_SCHEME=http
VITE_REVERB_APP_KEY="${REVERB_APP_KEY}"
VITE_REVERB_HOST="${REVERB_HOST}"
VITE_REVERB_PORT="${REVERB_PORT}"
VITE_REVERB_SCHEME="${REVERB_SCHEME}"
```

> Para recibir el webhook de Wompi en local, expón el puerto 8000 con un túnel (por ejemplo, ngrok) y registra `https://<tu-túnel>/webhook/wompi` como URL de eventos en el panel de Wompi.

---

## Usuarios de prueba

Los *seeders* crean estas cuentas, todas con correo verificado y la contraseña **`12345`**:

| Rol | Correo |
|---|---|
| Administrador | `admin@gmail.com` |
| Ganadero | `ganadero@gmail.com` |
| Veterinario | `veterinario@gmail.com` |
| Comprador | `comprador@gmail.com` |

También se crean 20 fincas con lotes, alrededor de 470 animales con historial sanitario y de pesajes, veterinarios adicionales (`*@vet.com`), transacciones y pedidos en distintos estados.

> ⚠️ Estas credenciales son solo para desarrollo. No ejecutes los *seeders* en producción.

---

## Comandos útiles

| Comando | Descripción |
|---|---|
| `composer setup` | Instalación completa (dependencias, `.env`, key, migraciones y build) |
| `composer dev` | Servidor, cola, logs (Pail) y Vite en paralelo |
| `composer test` | Ejecuta la suite de pruebas |
| `npm run dev` | Solo el servidor de Vite |
| `npm run build` | Compila los assets para producción |
| `npm run lint` | ESLint con autocorrección sobre `resources/js` |
| `php artisan migrate:fresh --seed` | Reinicia la base de datos con los datos de prueba |
| `php artisan queue:work` | Procesa la cola de trabajos |
| `php artisan schedule:work` | Ejecuta el scheduler en local |
| `php artisan reverb:start` | Inicia el servidor de WebSockets (opcional) |
| `./vendor/bin/pint` | Formatea el código PHP |

---

## Despliegue con Docker

El repositorio incluye un `Dockerfile` basado en `php:8.2-apache` que instala las extensiones de PHP, Composer y Node 20, compila los assets con Vite y apunta Apache a `public/`.

```bash
docker build -t hatomanager .
docker run -p 8080:80 --env-file .env hatomanager
```

> La imagen no incluye la base de datos: configura `DB_HOST` para que apunte a un servicio MySQL externo y ejecuta `php artisan migrate --force` dentro del contenedor. En producción (`APP_ENV=production`) la aplicación fuerza HTTPS y confía en los encabezados `X-Forwarded-*` del proxy.

---

## Rutas principales

| Ruta | Acceso | Descripción |
|---|---|---|
| `/` | Público | Inicio de la tienda |
| `/ventas`, `/sales/animales/{id}` | Público | Catálogo y ficha pública del animal |
| `/contacto`, `/sobre-nosotros`, `/ayuda`, `/terminos-de-uso`, `/privacidad` | Público | Páginas informativas |
| `/carrito`, `/checkout`, `/checkout/resultado` | Autenticado | Carrito y pago |
| `/my-orders` | Autenticado | Historial de pedidos del comprador |
| `/webhook/wompi` | Wompi (firma) | Eventos de transacciones |
| `/select-farm`, `/mis-fincas` | Ganadero, Veterinario | Finca activa y gestión de fincas |
| `/animals`, `/sanidad`, `/weight-records` | Ganadero, Veterinario | Hato, sanidad y pesajes |
| `/animals/{id}/certificado`, `/fincas/{id}/certificado-lote` | Ganadero, Veterinario | Certificados en PDF |
| `/dashboard`, `/paddocks`, `/my-veterinarians` | Ganadero | Dashboard, lotes y veterinarios |
| `/mis-transacciones`, `/my-sales` | Ganadero | Ventas/compras y pedidos recibidos |
| `/users`, `/roles`, `/permissions` | Admin | Seguridad de la plataforma |
| `/breeds`, `/animal-categories`, `/type-grasses`, `/productive-stages`, `/weight-methods` | Admin | Catálogos maestros |
| `/admin/contact` | Admin | Mensajes del formulario de contacto |
| `/up` | Público | *Health check* |

---

## Pruebas

```bash
composer test
# o
php artisan test
```

La suite cubre los flujos de autenticación de Breeze/Fortify: login, registro, verificación de correo, confirmación, reseteo y actualización de contraseña, y el perfil.

---

## Autores

Proyecto desarrollado para **La Feria del Software**.

- **Daniel Ramírez Navarro** — [@Daniel-RN24](https://github.com/Daniel-RN24)
- **Mateo Hoyos Hernández** — [@plapers2](https://github.com/plapers2)

---

<p align="center">
  Hecho con 💚 para el campo colombiano.
</p>
