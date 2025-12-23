# Docker + Java 21 + Spring Boot + PostgreSQL

Entorno de desarrollo completo con **hot reload automático** y **migraciones versionadas** de base de datos.

## 🚀 Stack Tecnológico

- **Java 21** (Eclipse Temurin JDK)
- **Spring Boot 3.2.1**
- **PostgreSQL 16**
- **Hibernate (JPA)** - ORM
- **Flyway** - Migraciones de BD
- **Maven** - Gestor de dependencias
- **Docker Compose** - Orquestación

## ✨ Características

✅ **Hot Reload Automático** - Cambios en Java se aplican en 5 segundos
✅ **Migraciones Versionadas** - Control total de cambios en BD con Flyway
✅ **API REST Completa** - CRUD de usuarios implementado
✅ **PostgreSQL Persistente** - Datos guardados en volumen Docker
✅ **Scripts de Monitoreo** - Auto-recompilación cuando editas código

---

## 🏃 Inicio Rápido

### 1. Levantar servicios

```bash
cd CURSO-JAVA-REACT
docker-compose up --build
```

**Espera a ver:**
```
postgres-db         | database system is ready to accept connections
spring-boot-backend | Started Application in X.XXX seconds
```

### 2. Probar que funciona

```bash
# Backend funcionando
curl http://localhost:8080/api/users

# Crear usuario
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","email":"admin@example.com","fullName":"Administrator"}'
```

---

## 🔌 Conexiones y Puertos

### Backend API
- **URL Base**: `http://localhost:8080`
- **Swagger/Docs**: No configurado (puedes agregar SpringDoc)

### PostgreSQL
- **Host**: `localhost`
- **Puerto**: `5432`
- **Database**: `mydb`
- **Usuario**: `postgres`
- **Password**: `postgres`

**Conectar desde terminal:**
```bash
docker exec -it postgres-db psql -U postgres -d mydb
```

**Comandos útiles en psql:**
```sql
\dt                    -- Listar tablas
\d users              -- Ver estructura de tabla users
SELECT * FROM users;  -- Ver usuarios
SELECT * FROM flyway_schema_history;  -- Ver migraciones aplicadas
\q                    -- Salir
```

---

## 📡 API Endpoints

### Users API

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/api/users` | Listar todos los usuarios |
| GET | `/api/users/{id}` | Obtener usuario por ID |
| GET | `/api/users/username/{username}` | Buscar por username |
| POST | `/api/users` | Crear usuario |
| PUT | `/api/users/{id}` | Actualizar usuario |
| DELETE | `/api/users/{id}` | Eliminar usuario |

### Otros

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/api/hello` | Mensaje de prueba |
| GET | `/api/status` | Estado del servicio |

---

## 🔥 Hot Reload - Paso a Paso

### Cómo Funciona

1. **Editas un archivo `.java`** (controller, service, entity)
2. **Guardas** (Cmd+S / Ctrl+S)
3. **Script detecta el cambio** (revisa cada 2 segundos)
4. **Maven compila** automáticamente
5. **DevTools reinicia** Spring Boot
6. **Cambios aplicados** en ~5-10 segundos

### Ejemplo: Agregar Endpoint

**1. Edita `HelloController.java`:**

```java
@GetMapping("/nueva-ruta")
public Map<String, String> nuevaRuta() {
    Map<String, String> response = new HashMap<>();
    response.put("mensaje", "¡Funciona!");
    return response;
}
```

**2. Guarda el archivo**

**3. Ver logs:**
```bash
docker-compose logs -f backend
```

Verás:
```
🔄 Cambios detectados:
   - src/main/java/com/example/demo/controller/HelloController.java
📦 Recompilando...
✨ Compilación exitosa! DevTools recargará automáticamente...
Restarting due to 1 class path change
Started Application in 0.XXX seconds
```

**4. Probar:**
```bash
curl http://localhost:8080/api/nueva-ruta
```

---

## 🗄️ Migraciones con Flyway

### ¿Qué es Flyway?

Flyway versiona los cambios de tu base de datos, como Git pero para SQL.

**Antes (Hibernate auto):**
- ❌ Sin historial
- ❌ Sin control
- ❌ Peligroso en producción

**Ahora (Flyway):**
- ✅ Cada cambio versionado (V1, V2, V3...)
- ✅ Historial completo
- ✅ Seguro y predecible

### Ver Migraciones Aplicadas

```bash
docker exec postgres-db psql -U postgres -d mydb -c "SELECT version, description, installed_on FROM flyway_schema_history;"
```

Resultado:
```
version | description          | installed_on
--------|----------------------|-------------------
1       | create users table   | 2025-12-23 13:52:34
2       | add phone number...  | 2025-12-23 13:52:34
```

### Crear Nueva Migración

**1. Crear archivo:**

Ubicación: `backend/src/main/resources/db/migration/V3__add_status_to_users.sql`

Reglas de nombre:
- `V` = Version (mayúscula)
- `3` = Número secuencial
- `__` = Doble guión bajo
- `add_status_to_users` = Descripción

**2. Escribir SQL:**

```sql
-- Migración: Agregar campo status a usuarios
-- Fecha: 2025-12-23

ALTER TABLE users
ADD COLUMN status VARCHAR(20) DEFAULT 'active';

COMMENT ON COLUMN users.status IS 'Estado: active, inactive, banned';
```

**3. Reiniciar backend:**

```bash
docker-compose restart backend
```

**4. Verificar aplicación:**

```bash
docker-compose logs backend | grep "Migrating schema"
```

Verás:
```
Migrating schema "public" to version "3 - add status to users"
Successfully applied 1 migration
```

**5. Actualizar entidad Java:**

```java
@Entity
public class User {
    // ... campos existentes ...
    
    private String status;
    
    // getters y setters
    public String getStatus() { return status; }
    public void setStatus(String status) { this.status = status; }
}
```

**6. Guardar → Hot reload → ¡Listo!**

### Ejemplo de Migración Compleja

```sql
-- V4__create_products_table.sql

CREATE TABLE products (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    user_id BIGINT REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_products_user ON products(user_id);
CREATE INDEX idx_products_name ON products(name);
```

---

## 🛠️ Comandos Docker

```bash
# Levantar todo
docker-compose up

# Levantar en background
docker-compose up -d

# Reconstruir
docker-compose up --build

# Ver logs
docker-compose logs -f

# Logs solo de backend
docker-compose logs -f backend

# Ver estado
docker-compose ps

# Reiniciar solo backend
docker-compose restart backend

# Detener todo
docker-compose down

# Detener y eliminar datos (⚠️ borra la BD)
docker-compose down -v
```

---

## 📁 Estructura del Proyecto

```
CURSO-JAVA-REACT/
├── docker-compose.yml
├── README.md
└── backend/
    ├── Dockerfile
    ├── pom.xml
    ├── mvnw
    ├── start-dev.sh          ← Script de hot reload
    └── src/main/
        ├── java/com/example/demo/
        │   ├── Application.java
        │   ├── controller/
        │   │   ├── HelloController.java
        │   │   └── UserController.java
        │   ├── entity/
        │   │   └── User.java
        │   ├── repository/
        │   │   └── UserRepository.java
        │   └── service/
        │       └── UserService.java
        └── resources/
            ├── application.properties
            └── db/migration/
                ├── V1__create_users_table.sql
                └── V2__add_phone_number_example.sql
```

---

## 🔧 Configuración Importante

### application.properties

```properties
# Database
spring.datasource.url=jdbc:postgresql://postgres:5432/mydb
spring.datasource.username=postgres
spring.datasource.password=postgres

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=validate  # ← Flyway maneja las migraciones
spring.jpa.show-sql=true

# Flyway
spring.flyway.enabled=true
spring.flyway.baseline-on-migrate=true
spring.flyway.locations=classpath:db/migration

# DevTools (Hot Reload)
spring.devtools.restart.enabled=true
spring.devtools.livereload.enabled=true
```

### pom.xml (Dependencias clave)

```xml
<!-- Spring Data JPA -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- PostgreSQL Driver -->
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>

<!-- Flyway Migrations -->
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>

<!-- DevTools (Hot Reload) -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

---

## 🧪 Testing Completo

### 1. Crear Usuario

```bash
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john",
    "email": "john@example.com",
    "fullName": "John Doe"
  }'
```

Respuesta:
```json
{
  "id": 1,
  "username": "john",
  "email": "john@example.com",
  "fullName": "John Doe",
  "createdAt": "2025-12-23T13:53:39.529021",
  "updatedAt": "2025-12-23T13:53:39.529089"
}
```

### 2. Listar Usuarios

```bash
curl http://localhost:8080/api/users
```

### 3. Actualizar Usuario

```bash
curl -X PUT http://localhost:8080/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john",
    "email": "john.doe@example.com",
    "fullName": "John M. Doe"
  }'
```

### 4. Eliminar Usuario

```bash
curl -X DELETE http://localhost:8080/api/users/1
```

---

## 🚨 Troubleshooting

### Backend no levanta

```bash
# Ver logs completos
docker-compose logs backend

# Reconstruir desde cero
docker-compose down -v
docker-compose up --build
```

### Hot reload no funciona

```bash
# Verificar script corriendo
docker-compose logs backend | grep "Monitoreando"

# Debería ver:
# 👀 Monitoreando cambios en src/main/java...

# Si no, reiniciar:
docker-compose restart backend
```

### No se pueden aplicar migraciones

```bash
# Ver error exacto
docker-compose logs backend | grep -i "flyway"

# Si dice "table already exists", empezar de cero:
docker-compose down -v
docker-compose up --build
```

### Puerto ya en uso

```bash
# Ver qué proceso usa el puerto
lsof -i :8080
lsof -i :5432

# Matar proceso o cambiar puerto en docker-compose.yml
```

---

## 📚 Próximos Pasos Recomendados

- [ ] Agregar Spring Security + JWT
- [ ] Implementar paginación en listados
- [ ] Agregar validación con `@Valid` y Bean Validation
- [ ] Configurar Swagger/SpringDoc para documentar API
- [ ] Tests con `@DataJpaTest` y `@WebMvcTest`
- [ ] CI/CD con GitHub Actions
- [ ] Agregar más entidades (Product, Order, etc.)

---

## 📄 Licencia

MIT

---

## ✨ Resumen

**Para empezar:**
```bash
docker-compose up
```

**Hot reload:**
- Edita `.java` → Guarda → Espera 5 seg → ¡Listo!

**Migraciones:**
- Crea `VX__nombre.sql` → `docker-compose restart backend`

**Todo funcionando:**
- Backend: http://localhost:8080
- PostgreSQL: localhost:5432
- Logs: `docker-compose logs -f`

**¡Eso es todo!** 🎯
