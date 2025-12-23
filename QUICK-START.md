# 🚀 Guía Rápida: Levantar y Conectar Todo

## 📦 Qué tienes instalado

- **Java 25 EA** (Early Access - última versión)
- **Spring Boot 4.0.0** (versión más reciente - Nov 2025)
- **Spring Framework 7.0.1**
- **PostgreSQL 16**
- **Flyway** (migraciones de BD)
- **Hot Reload** (backend automático)

## ⚡ Inicio Rápido

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

### 2. Verificar que funciona

```bash
# Backend funcionando
curl http://localhost:8080/api/users

# Ver logs
docker-compose logs -f backend
```

---

## 🔌 Conexiones

### Backend API

- **URL**: `http://localhost:8080`
- **Endpoint prueba**: `http://localhost:8080/api/hello`

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

**Dentro de psql:**

```sql
\dt                    -- Ver tablas
SELECT * FROM users;   -- Ver usuarios
\q                     -- Salir
```

---

## 🔄 Hot Reload

### Backend (Java 25)

1. Edita cualquier `.java`
2. Guarda (Cmd+S)
3. Espera 5 segundos
4. Cambios aplicados automáticamente

### Ver que funciona:

```bash
docker-compose logs -f backend
# Verás: "Restarting due to 1 class path change"
```

---

## 🗄️ Migraciones (Flyway)

### Ver historial

```bash
docker exec postgres-db psql -U postgres -d mydb -c "SELECT version, description, installed_on FROM flyway_schema_history;"
```

### Crear nueva migración

**1. Crear archivo:**
`backend/src/main/resources/db/migration/V3__descripcion.sql`

**2. Escribir SQL:**

```sql
ALTER TABLE users ADD COLUMN status VARCHAR(20) DEFAULT 'active';
```

**3. Aplicar:**

```bash
docker-compose restart backend
```

**4. Actualizar Java:**

```java
// backend/src/main/java/com/example/demo/entity/User.java
@Entity
public class User {
    // ... otros campos
    private String status;
    // getters y setters
}
```

---

## 🧪 Probar API

### Crear usuario

```bash
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"username":"test","email":"test@example.com","fullName":"Test User"}'
```

### Listar usuarios

```bash
curl http://localhost:8080/api/users
```

### Ver usuario por ID

```bash
curl http://localhost:8080/api/users/1
```

---

## 🛑 Detener todo

```bash
# Detener servicios
docker-compose down

# Detener Y eliminar datos de BD
docker-compose down -v
```

---

## 🔧 Comandos Útiles

```bash
# Ver estado
docker-compose ps

# Logs de todo
docker-compose logs -f

# Solo logs de backend
docker-compose logs -f backend

# Solo logs de PostgreSQL
docker-compose logs -f postgres

# Reiniciar solo backend
docker-compose restart backend

# Reconstruir todo
docker-compose up --build
```

---

## ✨ Resumen

1. `docker-compose up` → Levanta todo
2. `http://localhost:8080` → Backend API
3. `localhost:5432` → PostgreSQL
4. Editar `.java` → Hot reload automático
5. Crear `VX__nombre.sql` → Nueva migración
6. `docker-compose restart backend` → Aplicar cambio

**Stack:** Java 25 EA + Spring Boot 4.0.0 + PostgreSQL 16 + Flyway

**¡Eso es todo!** 🎯
