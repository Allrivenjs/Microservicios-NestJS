
# 🚀 Sistema de Gestión de Pedidos - Microservicios con NestJS (Arquitectura Hexagonal)

## 📝 Descripción del Proyecto

Este proyecto consiste en el desarrollo de una API basada en microservicios para la gestión de pedidos en un sistema de e-commerce, implementada con **NestJS**, **Prisma**, **PostgreSQL** y **NATS** para mensajería. Se emplea **arquitectura hexagonal (puertos y adaptadores)** para desacoplar el dominio de los detalles de infraestructura, facilitando el mantenimiento, la escalabilidad y los tests.  
Todo el ecosistema es fácilmente desplegable usando **Docker Compose**.

---

## 🏗️ Arquitectura General

### 📐 Arquitectura Hexagonal

Cada microservicio sigue el patrón **hexagonal**, separando:

- **Dominio:** Entidades, servicios de dominio, interfaces (puertos).
- **Aplicación:** Casos de uso.
- **Infraestructura:** Adaptadores para bases de datos, mensajería, APIs externas, controladores.
- **Interfaz:** Controladores HTTP o listeners de eventos.

### 🗂️ Estructura del Mono-repo

```
/ecommerce-microservices/
│
├── apps/
│   ├── users/          # Microservicio de usuarios
│   ├── orders/         # Microservicio de pedidos
│   └── api-gateway/    # (opcional)
│
├── libs/               # Código compartido: DTOs, eventos, validadores
│
├── docker-compose.yml  # Orquestador de servicios
├── .env.*              # Variables por microservicio
├── README.md
└── ...
```

### 📦 Componentes Principales

- **Microservicio de Usuarios:** Registro, autenticación (JWT), perfil.
- **Microservicio de Pedidos:** Crear, listar, cambiar estado (pendiente, en proceso, completado).
- **NATS:** Mensajería entre microservicios.
- **PostgreSQL:** Base de datos relacional.
- **Prisma:** ORM para acceso y migración de datos.
- **Docker Compose:** Orquestación de contenedores.
- **Jest:** Pruebas unitarias.
- **Swagger:** Documentación automática de endpoints.

---

## ⚙️ Tecnologías y Librerías Principales

- [NestJS](https://nestjs.com/): Framework backend.
- [Prisma](https://www.prisma.io/): ORM para Node.js y TypeScript.
- [PostgreSQL](https://www.postgresql.org/): Base de datos relacional.
- [NATS](https://nats.io/): Broker de mensajería.
- [bcryptjs](https://www.npmjs.com/package/bcryptjs): Hash de contraseñas.
- [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken): Autenticación JWT.
- [class-validator/class-transformer](https://docs.nestjs.com/techniques/validation): Validación de DTOs.
- [@nestjs/swagger](https://docs.nestjs.com/openapi/introduction): Documentación API.
- [Jest](https://jestjs.io/): Testing.
- [Docker](https://www.docker.com/), [Docker Compose](https://docs.docker.com/compose/): Contenerización y orquestación.

---

## 🏁 Paso a Paso: Desarrollo y Despliegue

### 1. **Clonar el repositorio**
```bash
git clone https://github.com/tuusuario/ecommerce-microservices.git
cd ecommerce-microservices
```

### 2. **Instalación de dependencias**
```bash
npm install
```

### 3. **Configuración de variables de entorno**
Copia y edita los archivos `.env.example` en cada microservicio:
```
cp .env.users.example .env.users
cp .env.orders.example .env.orders
```
Asegúrate de configurar las credenciales de DB, JWT, NATS, etc.

### 4. **Despliegue local con Docker Compose**
```bash
docker-compose up --build
```
Esto levanta:
- Base de datos PostgreSQL
- Broker de NATS
- users-ms (`localhost:3001`)
- orders-ms (`localhost:3002`)
- (opcional) api-gateway (`localhost:3000`)

### 5. **Migraciones Prisma**
Entra a cada microservicio y ejecuta migraciones:
```bash
cd apps/users
npx prisma migrate deploy
cd ../orders
npx prisma migrate deploy
```

### 6. **Acceso a la documentación Swagger**
- Usuarios: [http://localhost:3001/api](http://localhost:3001/api)
- Pedidos: [http://localhost:3002/api](http://localhost:3002/api)

### 7. **Ejecutar pruebas unitarias**
```bash
npm run test:users
npm run test:orders
```

---

## 🚦 Estructura Hexagonal (por microservicio)

```
/src/
│
├── domain/            # Entidades, servicios de dominio, interfaces (puertos)
│   ├── entities/
│   ├── services/
│   └── repositories/
│
├── application/       # Casos de uso, lógica de aplicación
│   └── use-cases/
│
├── infrastructure/    # Adaptadores: DB (Prisma), mensajería, providers
│   ├── prisma/
│   ├── nats/
│   └── ...
│
├── interfaces/        # Controladores HTTP, listeners NATS, DTOs
│   ├── controllers/
│   ├── listeners/
│   └── dto/
│
└── main.ts
```

---

## 🛠️ Patrones y Buenas Prácticas Aplicadas

- **Hexagonal Architecture (Ports & Adapters):**  
  Aisla la lógica de dominio del framework y la infraestructura.
- **Principios SOLID:**  
  Código desacoplado, single responsibility, dependencias por interfaces.
- **DTOs y validación estricta** en todos los endpoints.
- **Testing first:**  
  Pruebas unitarias de casos de uso y de integración de endpoints críticos.
- **Mensajería asíncrona**: eventos de dominio (ejemplo: `UserCreatedEvent`), permite escalabilidad y bajo acoplamiento.
- **Manejo de errores centralizado**.
- **Documentación automática** de la API.

---

## 🔄 Comunicación entre microservicios

- **Autenticación:**  
  Users-ms genera el JWT y expone el perfil autenticado.
- **Pedidos:**  
  Orders-ms valida el JWT y consume el ID de usuario para operaciones.
- **Mensajería:**  
  Ejemplo: Cuando un usuario se crea, users-ms emite un evento `UserCreated` a través de NATS. Orders-ms puede escuchar este evento y crear recursos asociados si es necesario.

---

## 🧪 Testing

- Cada microservicio incluye pruebas unitarias y de integración básicas.
- Pruebas para:
  - Creación de usuario
  - Login y obtención de perfil autenticado
  - Creación y listado de pedidos
  - Cambio de estado de pedidos
  - Validación de reglas de negocio
- Cobertura mínima recomendada: **80%**

**Ejecutar pruebas:**
```bash
npm run test:users
npm run test:orders
```

---

## 📝 Ejemplo de flujo de trabajo

1. **Registrar usuario:**  
   `POST /users/register`
2. **Autenticar usuario:**  
   `POST /auth/login` (recibe JWT)
3. **Crear pedido:**  
   `POST /orders` (requiere JWT en headers)
4. **Listar pedidos:**  
   `GET /orders` (requiere JWT)
5. **Cambiar estado pedido:**  
   `PATCH /orders/:id/status` (requiere JWT)

## 📬 Autor

Jaime Ruiz  

