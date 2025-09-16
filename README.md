# 📚 Gestor de Estudiantes con Docker Compose

Un laboratorio completo para aprender a orquestar aplicaciones full-stack con contenedores. Este proyecto expone una API REST construida con Spring Boot, un frontend moderno en Angular y una base de datos MySQL, todo empaquetado y listo para desplegarse con Docker Compose.

## 🧰 Tecnologías principales
- **Angular 18** con componentes standalone, Reactive Forms y consumo de API mediante `HttpClient`.
- **Spring Boot 3.4** + **Spring Data JPA** para la capa de servicios y persistencia.
- **MySQL 8** como motor relacional con almacenamiento persistente en volumen Docker.
- **Docker & Docker Compose** para construir imágenes multi-stage y orquestar los servicios.
- **Nginx** como servidor estático de la aplicación Angular ya compilada.
- **Maven** y **Temurin JRE 24** para compilar y ejecutar el backend de manera optimizada.

## 🏗️ Arquitectura de contenedores
```text
┌────────────┐        ┌──────────────┐        ┌──────────────┐
│  Frontend  │ <----> │   Backend    │ <----> │ Base de datos│
│ Angular +  │        │ Spring Boot  │        │   MySQL 8    │
│   Nginx    │        │   Puerto 8090│        │   Puerto 3307│
└────────────┘        └──────────────┘        └──────────────┘
```

| Servicio  | Imagen base / build | Puerto expuesto | Descripción |
|-----------|---------------------|-----------------|-------------|
| `frontend` | `node:alpine` → `nginx:alpine` | `80:80` | Compila Angular y sirve los archivos estáticos con Nginx. |
| `backend`  | `maven:3.9.9-eclipse-temurin-24` → `eclipse-temurin:24-jre-alpine` | `8090:8080` | API REST para gestionar estudiantes, genera documentación automática con SpringDoc. |
| `db`       | `mysql:latest` | `3307:3306` | Motor MySQL con volumen `mysql_data` para persistencia. |

> 💡 Gracias a las variables de entorno declaradas en `docker-compose.yaml`, el backend se conecta al contenedor de MySQL (`jdbc:mysql://db:3306/ejemplo-db`) sin necesidad de modificar archivos de configuración.

## ✨ Funcionalidades implementadas
### Frontend Angular
- Formulario reactivo para crear estudiantes con validaciones de campos requeridos, longitud máxima y formato de correo.
- Listado dinámico que consume la API y muestra el estado de carga, errores y mensaje cuando no hay datos.
- Acciones de recarga y eliminación inmediata con confirmación, manteniendo la interfaz responsive y accesible.

### Backend Spring Boot
- API RESTful para consultar, crear y eliminar estudiantes sobre la entidad `Estudiante` mapeada con JPA.
- Servicio y repositorio desacoplados que aprovechan `JpaRepository` para operaciones CRUD.
- Configuración de CORS abierta (`@CrossOrigin`) para facilitar la comunicación con el frontend.
- Documentación OpenAPI lista en `http://localhost:8090/swagger-ui/index.html` mediante `springdoc-openapi`.

### Base de datos MySQL
- Creación automática de la tabla `estudiantes` gracias a `spring.jpa.hibernate.ddl-auto=update`.
- Volumen Docker (`mysql_data`) que conserva la información incluso si el contenedor se reinicia.

## 🚀 Puesta en marcha con Docker Compose
1. Clona este repositorio y entra a la carpeta raíz.
2. Construye y levanta los servicios:
   ```bash
   docker-compose up --build
   ```
3. Una vez que los contenedores estén levantados:
   - Frontend disponible en `http://localhost/`.
   - API REST en `http://localhost:8090/api`.
   - MySQL escuchando en `localhost:3307` (usuario: `usuario`, contraseña: `usuario123`).
4. Para detener la stack y liberar recursos:
   ```bash
   docker-compose down
   ```

## 🔌 Endpoints principales
| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/saludo` | Endpoint de prueba que responde "Hola Mundo". |
| `GET` | `/api/estudiantes` | Obtiene el listado completo de estudiantes. |
| `POST` | `/api/estudiantes` | Crea un nuevo estudiante (JSON con `nombres`, `apellidos`, `email`). |
| `DELETE` | `/api/estudiantes/{id}` | Elimina un estudiante existente por su identificador. |

## 🧑‍💻 Desarrollo local sin Docker
Si prefieres ejecutar los componentes por separado:

**Backend**
```bash
cd backend
./mvnw spring-boot:run  # o mvn spring-boot:run si tienes Maven instalado
```
Asegúrate de tener una instancia de MySQL disponible en `localhost:3307` con las mismas credenciales del proyecto.

**Frontend**
```bash
cd frontend
npm install
npm start  # levanta la app en http://localhost:4200
```
Edita `src/environments/environment.ts` si necesitas apuntar a otra URL para la API.

## 📁 Estructura relevante del repositorio
```
Reto_3-Docker/
├── docker-compose.yaml
├── backend/
│   ├── Dockerfile
│   ├── pom.xml
│   └── src/main/java/co/edu/udem/ejemplodockercompose/
│       ├── controller/EstudianteController.java
│       ├── service/EstudianteService.java
│       ├── repository/EstudianteRepository.java
│       └── model/Estudiante.java
└── frontend/
    ├── Dockerfile
    ├── nginx/default.conf
    └── src/app/
        ├── app.component.{ts,html,css}
        ├── services/estudiantes.service.ts
        └── models/estudiante.model.ts
```

