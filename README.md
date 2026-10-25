# grades — Homework 7

Registro personal de materias y sus actividades evaluables. Cada actividad guarda título, tipo, calificación de 0 a 100, fecha y observaciones opcionales. Incluye CRUD de materias y actividades.

Proyecto independiente con **Express + Sequelize + EJS + MySQL/MariaDB**, como adaptación tecnológica solicitada para esta entrega. Express sustituye el controlador y las rutas de Laravel, EJS proporciona las vistas y Sequelize los modelos y migraciones. No utiliza Laravel 7 ni Artisan.

Repositorio: https://github.com/LeonardoH369/grades

[Reporte editable en Google Docs](https://docs.google.com/document/d/10QFJfYKAWcn3yB7cDECyJbRfuPHt_XH9LGLwhtVnMB0/edit)

## Instalación

Requisitos: Node.js 22.13 o superior y MySQL 8.0.16+ o MariaDB 10.4+. Probado con Node.js 24.19 y MariaDB 10.4.32.

```powershell
npm ci
Copy-Item .env.example .env
# Configurar DB_HOST, DB_PORT, DB_NAME, DB_USER y DB_PASSWORD en .env.
# Iniciar el servicio MySQL/MariaDB.
npm run db:create
npm run db:migrate
npm run db:seed
npm start
```

Abrir http://localhost:3003. No ejecutar `db:create` si la base ya existe. Las migraciones y semillas se registran en SequelizeMeta y SequelizeData; `db:seed` no vuelve a ejecutar las semillas ya registradas. No se usa sync con force. `.env` y `node_modules` se excluyen de Git. El ejemplo de conexión usa puerto de BD 3306; la validación local se realizó en una instancia aislada con puerto 3307.

## Estructura MVC

`src/models/index.js`: Subject tiene muchas Activity y cada actividad pertenece a una materia. `SubjectController.js` y `ActivityController.js` implementan sus operaciones. La FK protege referencias y un CHECK limita las notas a 0–100.

`src/views/` contiene las vistas EJS; `src/routes/web.js`, las rutas; `src/migrations/`, el esquema; `src/seeders/`, los ejemplos; `public/`, los estilos y recursos. El servidor escucha exclusivamente en 127.0.0.1: aplicación local de uso académico sin autenticación.

## Rutas

GET `/subjects`, `/subjects/create`, `/subjects/:subjectId`, `/subjects/:subjectId/edit` y `/subjects/:subjectId/delete`. POST `/subjects`, `/subjects/:subjectId/update` y `/subjects/:subjectId/delete`. Actividades: GET `/subjects/:subjectId/activities/create`, `/subjects/:subjectId/activities/:id/edit`, `/subjects/:subjectId/activities/:id/delete`; POST `/subjects/:subjectId/activities`, `/subjects/:subjectId/activities/:id/update` y `/subjects/:subjectId/activities/:id/delete`.

Se usan formularios HTML con POST para escrituras y redirección 303 tras guardar. Abrir la confirmación de eliminación con GET no cambia la base. Los formularios incluyen token CSRF firmado y cookie HttpOnly/SameSite. EJS escapa el contenido mostrado, se validan los datos en el servidor y se usan operaciones del ORM sin concatenar SQL con entradas del usuario.

## Reglas de datos

Los nombres de materias son únicos, incluso después de una baja lógica. Las notas admiten hasta dos decimales; se comprueban fechas reales. El promedio visible es simple, sin ponderación. El campo opcional de la consigna se interpreta como observaciones. Al eliminar una materia se retiran también sus actividades, en una transacción. Todas las notas de la semilla son ficticias.

Las eliminaciones son bajas lógicas mediante `deleted_at`. Los registros retirados dejan de aparecer en las consultas normales; el modelo permite recuperarlos mediante `restore()` en una operación administrativa de desarrollo. No se incluye interfaz de papelera.

## Pruebas

```powershell
$env:DB_NAME = 'grades_test'
npm run db:create
npm run db:migrate
npm run db:seed
npm test
Remove-Item Env:DB_NAME
```

`npm test` solo permite las bases `superheroes_test` o `grades_test`; configurar la conexión con un nombre propio de este proyecto. 6 pruebas aprobadas. Se verifican lecturas y escrituras reales, errores de validación, referencias, bajas, escape HTML y protección CSRF. Las pruebas crean y limpian sus propios registros de ejemplo; ejecutarlas sobre una base de pruebas migrada y sembrada.

## Evidencias

Capturas originales en [docs/screenshots](docs/screenshots). En `docs/screenshots/report` hay recortes de las mismas capturas para mejorar la lectura en el reporte, sin modificar datos ni controles. Los registros utilizados para demostrar las operaciones son ficticios.

## Referencias

- https://expressjs.com/en/guide/routing/
- https://sequelize.org/docs/v6/core-concepts/model-querying-basics/
- https://sequelize.org/docs/v6/core-concepts/paranoid/
- https://ejs.co/

## Entrega actual verificada

[Reporte editable con portada y capturas](docs/Homework_7_Leonardo_Hinojosa_Castro.docx) · [Capturas actuales](docs/capturas-entrega) · [Verificación adicional con MySQL](docs/ENTREGA_ACTUAL.md).

La aceptación académica del cambio de framework debe confirmarse con el docente.
