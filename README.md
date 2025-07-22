# UserManager

Sistema Backend para Gestión de Usuarios con Seguridad y Tareas Asíncronas

---

## Descripción del proyecto

UserManager Pro es un sistema backend robusto y seguro para la gestión integral de usuarios. Permite registro, autenticación con tokens JWT, recuperación de contraseña, administración de roles y operaciones CRUD. Utiliza una base de datos embebida para almacenamiento y un sistema de colas para gestionar tareas en segundo plano, como el envío de correos electrónicos.

---

## Funciones principales

### Registro de usuario
- Crear nuevos usuarios con email y contraseña.
- Guardar contraseñas con hash seguro.
- Asignar rol por defecto (“user”).
- Enviar correo electrónico de bienvenida en tareas asíncronas.

### Inicio de sesión (Login)
- Autenticación con email y contraseña.
- Generación de token JWT con datos del usuario.
- Uso del token para autorizar solicitudes.

### Recuperación y cambio de contraseña
- Solicitar restablecimiento mediante email.
- Generar token temporal para validación.
- Enviar enlace de cambio de contraseña en tareas asíncronas.
- Cambiar contraseña tras validar token.

### Gestión de usuarios (CRUD)
- Listar, actualizar y eliminar usuarios.
- Restricciones basadas en roles (solo admins para algunas operaciones).
- Control de acceso con JWT y roles.

### Seguridad
- Hash de contraseñas con bcrypt.
- Validación y expiración de tokens JWT.
- Protección de rutas y manejo de errores.

### Tareas asíncronas
- Envío de emails (registro, recuperación de contraseña).
- Procesos en segundo plano que no bloquean la API.
- Escalabilidad y mejor rendimiento.

---

## Tecnologías usadas y funciones

| Tecnología         | Función/Tarea                                                                                      |
|--------------------|--------------------------------------------------------------------------------------------------|
| **FastAPI**        | Framework backend para exponer endpoints REST, manejar rutas, validaciones y lógica principal.   |
| **SQLite**         | Base de datos embebida para almacenar usuarios, roles y tokens temporales.                        |
| **SQLAlchemy**     | ORM para definir modelos y manejar consultas a SQLite de forma eficiente y segura.               |
| **Pydantic**       | Validación y serialización de datos para requests y responses.                                   |
| **PassLib (bcrypt)**| Hash y verificación segura de contraseñas.                                                      |
| **PyJWT / fastapi-jwt-auth** | Creación, firma y validación de tokens JWT para autenticación sin estado y autorización. |
| **RabbitMQ**       | Broker de colas para gestionar tareas asíncronas, como envío de emails.                          |
| **Celery**         | Framework para definir y ejecutar tareas en background con RabbitMQ como broker.                  |
| **SMTP / Servicio Email** | Servicio para envío efectivo de emails desde las tareas asíncronas.                         |
| **Uvicorn**        | Servidor ASGI para correr FastAPI con eficiencia.                                               |

---

## Tareas y responsabilidades de cada componente

### Backend API (FastAPI)
- Definir y validar endpoints para registro, login, recuperación y gestión de usuarios.
- Interactuar con SQLite mediante SQLAlchemy.
- Generar y validar tokens JWT.
- Autorizar rutas según roles.
- Encolar tareas en RabbitMQ para procesamiento asíncrono.

### Base de datos SQLite + SQLAlchemy
- Almacenar persistentemente usuarios, roles y tokens temporales.
- Mantener integridad y facilitar consultas.

### Sistema de colas (RabbitMQ + Celery)
- Gestionar tareas que requieren procesamiento en segundo plano.
- Ejecutar tareas como envío de emails sin bloquear la API.
- Gestionar reintentos y escalabilidad de tareas.

### Seguridad (PassLib + JWT)
- Hashear y verificar contraseñas.
- Emitir tokens JWT con expiración.
- Proteger endpoints mediante validación de tokens.
- Controlar acceso basado en roles.

### Servicio de Email (SMTP)
- Recibir solicitudes de envío desde tareas Celery.
- Entregar emails de confirmación y recuperación.
- Mantener plantillas y formatos.

---

## Beneficios de las tecnologías escogidas

- **FastAPI:** desarrollo rápido, documentación automática, async y comunidad activa.
- **SQLite:** simplicidad y portabilidad para desarrollo y prototipos.
- **RabbitMQ + Celery:** escalabilidad y mejor experiencia de usuario al no bloquear respuestas HTTP.
- **JWT:** autenticación ligera y sin estado.
- **PassLib:** seguridad en el manejo de contraseñas.
- **SQLAlchemy:** abstracción para base de datos que facilita mantenimiento y cambios futuros.

---

## Estructura sugerida del proyecto

```bash
/app
/api # Routers y endpoints
/auth # Lógica de autenticación, JWT y roles
/crud # Operaciones CRUD sobre usuarios
/models # Modelos SQLAlchemy
/schemas # Modelos Pydantic
/tasks # Definición de tareas asíncronas (Celery)
/utils # Funciones utilitarias (hash, token, email)
main.py # Inicialización FastAPI y configuración
celery_worker.py # Worker de Celery para ejecutar tareas
database.py # Configuración y conexión a SQLite
```

---

## Flujo básico de uso

1. Usuario se registra → API crea usuario + encola email de bienvenida.
2. Usuario inicia sesión → API verifica credenciales y retorna token JWT.
3. Usuario olvida contraseña → API genera token temporal + encola email con enlace.
4. Usuario restablece contraseña → API valida token + actualiza contraseña.
5. Usuario autenticado realiza acciones → API verifica token y rol para autorizar.
6. Emails y otras tareas largas se procesan en segundo plano con RabbitMQ + Celery.

---
