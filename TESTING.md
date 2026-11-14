# TESTING · CodeJalisco (Sprint 8)

Resultados de ejecuciones reales realizadas sobre el proyecto entregado (Node 22, Chrome sin interfaz, servidor real, datos temporales). Nada de lo marcado como correcto es una suposición; lo que no se pudo verificar está en la última sección.

## 1. Cómo ejecutar
```
npm test                                   # pruebas automáticas (las de la cuenta de la docente se omiten sin contraseña)
CJ_TEST_MAESTRA_PW='<contraseña>' npm test # incluye inicio de sesión y permisos de la cuenta de la docente
```
La contraseña se pasa solo en ejecución; no está en el código.

## 2. Pruebas automáticas (115 correctas, 0 fallidas)
| Archivo | Qué cubre | Resultado |
|---|---|---|
| `tests/smoke.js` | páginas, registro e inicio de sesión, roles, reportes con foto y coordenadas, folios, consulta (400/401/404), estados, paginación y filtros del panel, seguridad de rutas | 58 de 58 |
| `tests/integral.js` | ciclo completo: registro → reporte con foto y geolocalización → folio y consulta → panel → cambio de estado visto por el ciudadano; códigos HTTP | 17 de 17 |
| `tests/cuenta-maestra.js` | cuenta de la docente: se crea una vez, 3 reinicios reales sin duplicar, se restablece si se altera, se recrea sin `users.json`, concilia un registro previo, actualización desde versión anterior, archivo dañado | 19 de 19 |
| `tests/permisos-maestra.js` | la docente genera varios reportes, consulta folios, usa el panel y cambia estados; administradores y ciudadanos conservan sus permisos; reinicio; `MAESTRA_PASSWORD` | 21 de 21 |

## 3. Pruebas de aceptación (UAT) en navegador: 23 de 23
Sistema recién entregado (sin datos de prueba), Chrome sin interfaz, móvil (390 px) para el ciudadano y escritorio (1280 px) para la docente y el administrador.
| # | Escenario | Resultado |
|---|---|---|
| 1 | Sistema limpio: panel sin reportes (contadores en 0) | Correcto |
| 2 | Ciudadano: se registra y entra | Correcto |
| 3 | Ciudadano: genera su folio con foto y coordenadas (`CJ-2026-1001`) | Correcto |
| 4 | Ciudadano: consulta su folio (estado, fecha de creación, coordenadas y fotografía) | Correcto |
| 5 | Ciudadano: un segundo reporte muestra su folio existente | Correcto |
| 6 | Ciudadano: no entra al panel | Correcto |
| 7 | Docente: inicia sesión y ve su nombre | Correcto |
| 8 | Docente: el menú muestra Reportar problema y Panel de dependencia | Correcto |
| 9 | Docente: el panel muestra el reporte del ciudadano | Correcto |
| 10 | Docente: Inicio con "Crear un reporte", "Ir al Panel" y "Consultar un folio" | Correcto |
| 11 a 14 | Docente: ve el formulario y genera 2 reportes (`CJ-2026-1002`, `CJ-2026-1003`) | Correcto |
| 15 | Docente: consulta el folio del ciudadano con detalle completo | Correcto |
| 16 | Docente: ve "Mis reportes" (2) | Correcto |
| 17 | Docente: el panel muestra 3 reportes | Correcto |
| 18 | Docente: el salto Recibido → Atendido se bloquea con aviso | Correcto |
| 19 | Docente: actualiza el estado hasta Atendido y los contadores cambian | Correcto |
| 20 | Docente: búsqueda por colonia | Correcto |
| 21 | Ciudadano (móvil): ve el estado Atendido y el seguimiento de 4 pasos | Correcto |
| 22 | Administrador común: tiene panel y no ve Reportar problema | Correcto |
| 23 | Administrador común: `/reportar.html` lo redirige | Correcto |
No hubo errores de JavaScript en consola durante estas pruebas.

## 4. Validación responsiva
Se recorrieron 9 pantallas (inicio, login, registro, documentación, reportar, confirmación de folio, consultar, detalle de consulta y panel) en **móvil 390×844, tableta 768×1024 y escritorio 1280×800**: 27 de 27 sin desborde horizontal de la página (las tablas desplazan dentro de su tarjeta). Se revisaron visualmente capturas móviles del formulario y del panel. Solo se midió el desborde; no se midió el tamaño de los controles táctiles.

## 5. Errores encontrados y corregidos
| Error | Causa | Corrección |
|---|---|---|
| **"No me permite generar reportes"** (retroalimentación de la docente) | Su cuenta era de rol `administrador` y la API (`POST /api/reportes`), la página `/reportar.html` y el menú solo admitían `ciudadano` | Permiso `puedeReportar` en su cuenta (API, guarda de página, menú, Inicio y Consultar folio). Sin límite de un folio por cuenta. Los demás roles no cambian |
| "Cerrar sesión" se partía en dos líneas en móvil | El botón del encabezado no tenía `nowrap` | `white-space: nowrap` en el botón y la sesión ahora admite salto de línea |
| Una falla aislada en una ejecución de `cuenta-maestra.js` (no se repitió en 10 ejecuciones posteriores) | No se confirmó la causa; la sospecha es una colisión del puerto elegido al azar | Las pruebas piden un puerto libre al sistema; 6 ejecuciones consecutivas correctas |
| Los datos de prueba (3 reportes de ejemplo) estaban en la base entregada | Datos del Sprint 1 | `reportes.json` se entrega vacío; las pruebas usan `tests/fixtures/reportes-ejemplo.json` |
Hallazgos de sprints anteriores (salto a "Atendido", coordenadas inválidas, folio inexistente frente a error de conexión) están en `docs/sprint5.md`, `docs/sprint6.md` y `docs/pruebas.md`.

## 6. No verificado / limitaciones
- Prueba en teléfono o tableta físicos (solo se emularon los tamaños de pantalla).
- Inicio de sesión con Google (requiere credenciales reales).
- Despliegue en un hosting real; solo se probó en este entorno.
- Firefox y Safari (solo se usó Chromium).
- Pérdida de conexión a mitad de un registro.
- Carga real: la medición del Sprint 7 (3000 reportes) fue local y sin red.
- Fuera de alcance, según la documentación: base de datos relacional, almacenamiento de imágenes en la nube y notificaciones por correo.
