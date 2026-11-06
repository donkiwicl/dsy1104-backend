# dsy1104-backend

API REST de ejemplo (Spring Boot 3, Java 17) para el taller de despliegue en AWS de DSY1104 Full Stack 2.

| Endpoint | Descripción |
| --- | --- |
| `GET /api/productos` | Lista productos |
| `POST /api/productos` | Crea un producto (`{"nombre":"...","precio":1000}`) |
| `DELETE /api/productos/{id}` | Elimina un producto |
| `GET /actuator/health` | Estado de la aplicación |

## Perfiles

- **default**: base H2 en memoria. `mvn spring-boot:run`.
- **prod**: MySQL. Requiere las variables `DB_HOST`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` y `CORS_ORIGIN`.

```bash
SPRING_PROFILES_ACTIVE=prod DB_HOST=localhost DB_NAME=appdb DB_USER=root \
DB_PASSWORD=secreto CORS_ORIGIN=http://localhost:3000 java -jar target/app.jar
```

## Empaquetado

```bash
mvn clean package -DskipTests   # genera target/app.jar
docker build -t dsy1104-backend .
```

El entorno completo (MySQL + backend + frontend) se levanta desde el repositorio `dsy1104-meta`.
