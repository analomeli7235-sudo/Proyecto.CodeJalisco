# CodeJalisco
Plataforma ciudadana para reportar y dar seguimiento a problemas urbanos (baches, alumbrado, árboles caídos, basura y fugas de agua). Proyecto del curso *Proyecto*, Desarrollo de Sistemas Web. **Versión final (Sprint 8).**

## Requisitos
Node.js 18 o superior. No hay dependencias que instalar: el servidor usa solo módulos nativos (`npm install` no es necesario).

## Instalación y ejecución
```
cd codejalisco
cp .env.example .env     # opcional: puerto, contraseñas y Google (en Windows: copy .env.example .env)
npm start                # abre http://localhost:3000
npm test                 # pruebas automáticas (ver TESTING.md)
```
El sistema se entrega **limpio**: sin reportes de prueba ni fotografías. Al primer arranque crea `backend/data/users.json` con los administradores y la cuenta de la docente. Los primeros reportes tendrán folio `CJ-2026-1001`.

## Cuentas de acceso
| Cuenta | Correo | Contraseña | Puede |
|---|---|---|---|
| Docente (curso Proyecto) | lg2662@hotmail.com | la entregada por separado (configurable, ver abajo) | **Todo**: reportar (sin límite), consultar folios, panel, cambiar estados |
| Administrador 1, 2 y 3 | admin1@codejalisco.mx, admin2@…, admin3@… | `Admin#2026` (cámbiala con `ADMIN_PASSWORD`) | Panel de dependencia, cambiar estados, consultar cualquier folio |
| Ciudadano | el que cada persona cree en **Registrarse** (o con Google) | la que elija | Reportar (un folio por cuenta), consultar su folio |

La cuenta de la docente queda registrada automáticamente en cada arranque (sin duplicarse) y solo guarda un hash de su contraseña. Para cambiarla sin tocar código: `MAESTRA_PASSWORD=...` en `.env`. Más detalles en `docs/mantenimiento.md`.

## Funcionalidades
- Registro e inicio de sesión (correo y contraseña, o Google si se configura); sesiones con cookie HttpOnly; roles con permisos validados en el servidor.
- **Reportar problema**: categoría, descripción, municipio, colonia, calle, referencia, fotografía, correo, y **ubicación** (botón "Usar mi ubicación actual" o coordenadas manuales; opcional).
- **Folio único** `CJ-AAAA-NNNN` con pantalla de confirmación y **Consultar folio** (estado, fecha de creación, ubicación, fotografía y seguimiento).
- **Panel de dependencia**: tarjetas por estado, filtros por categoría y estado, búsqueda por folio o colonia, paginación y cambio de estado (Recibido, En revisión, En atención, Atendido; no se puede marcar Atendido sin pasar por En atención).
- Diseño adaptable a móvil, tableta y escritorio.

## Variables de entorno (`.env`)
`PORT`, `BASE_URL`, `ADMIN_PASSWORD`, `MAESTRA_PASSWORD`, `MAX_REPORTES_POR_USUARIO` (por defecto 1), `CJ_DATA` (carpeta de datos), `GOOGLE_CLIENT_ID` y `GOOGLE_CLIENT_SECRET`.
Google Sign-In: crea un ID de cliente OAuth (aplicación web) con la URI de redirección `http://localhost:3000/auth/google/callback` y pon ambas claves en `.env`. Quien entra con Google es siempre ciudadano.

## Estructura
```
backend/server.js                     API REST, sesiones, roles, Google OAuth y archivos estáticos protegidos
backend/data/categorias.json          catálogo de categorías
backend/data/reportes.json            reportes (se entrega vacío)
backend/data/cuentas-permanentes.json cuentas que se conservan en cada arranque (solo salt y hash)
backend/data/users.json               se genera al arrancar (no se versiona)
backend/data/uploads/                 fotografías de los reportes
backend/scripts/generar-hash.js       genera salt y hash para una contraseña
frontend/                             index, login, registro, reportar, consultar, panel y docs (+ css/ y js/)
tests/                                pruebas automáticas y datos de ejemplo para pruebas
docs/                                 documentación de los sprints, mantenimiento y pruebas
TESTING.md                            casos de prueba, resultados, errores encontrados y limitaciones
```
## Limitaciones conocidas
Los datos viven en archivos JSON y las fotografías en el disco local del servidor: suficiente para el prototipo académico, pero un entorno real necesitaría base de datos relacional y almacenamiento de imágenes en la nube. No hay notificaciones por correo. El inicio de sesión real requiere ejecutar Node.js (un hosting solo estático no lo soporta).
