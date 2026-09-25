# Arquitectura de Microservicios - Tiendas D1 (AutoCaja)

Sistema integral de AutoCaja implementado bajo una arquitectura de microservicios pura (Polyrepo). Este ecosistema aplica patrones de diseño de software avanzados para garantizar escalabilidad, bajo acoplamiento y alta disponibilidad, desarrollado como proyecto para el programa ADSO (SENA).

## 🏗️ Patrones de Arquitectura Implementados

* **API Gateway:** Único punto de entrada público (Node.js) que intercepta y enruta las peticiones hacia la red interna, ocultando la topología de los microservicios.
* **Database-per-Service:** Instancias de PostgreSQL independientes para cada dominio (Usuarios, Inventario, Compras). Ningún servicio tiene acceso directo a la base de datos de otro.
* **Comunicación Síncrona (HTTP/REST):** Interacción en tiempo real entre el MS Compras y el MS Inventario para validar y deducir stock de forma inmediata.
* **Comunicación Asíncrona (Event-Driven):** Uso de RabbitMQ como Message Broker para desacoplar el procesamiento de órdenes del envío de confirmaciones (MS Notificaciones).

## 💻 Stack Tecnológico

* **Core de Negocio:** Java, Spring Boot, Spring Data JPA, RestTemplate.
* **Enrutamiento y Eventos:** JavaScript, Node.js, Express, amqplib.
* **Frontend:** React, Axios.
* **Infraestructura y Persistencia:** Docker, Docker Compose, PostgreSQL, RabbitMQ.

---

## 🚀 Instrucciones de Ejecución Local

Para levantar el ecosistema completo, es necesario clonar los 7 repositorios de la organización y ejecutarlos en el siguiente orden estricto:

### 1. Levantar la Infraestructura Base
Ubicado en el repositorio raíz (`tiendas-d1-infra`), inicia los contenedores de las bases de datos (PostgreSQL) y el Message Broker (RabbitMQ):

```bash
docker-compose up -d
```
Espera a que los contenedores de PostgreSQL (puertos 5432, 5433, 5434) y RabbitMQ (5672) estén completamente activos.

2. Iniciar Microservicios de Negocio (Java)
En terminales independientes, ingresa a cada repositorio clonado e inicia la aplicación Spring Boot:

```bash
# MS Usuarios (Puerto 8081)
cd ms-usuarios
mvn spring-boot:run

# MS Inventario (Puerto 8082)
cd ms-inventario
mvn spring-boot:run

# MS Compras (Puerto 8083)
cd ms-compras
mvn spring-boot:run
```
3. Iniciar Servicios de Enrutamiento y Mensajería (Node.js)
En terminales separadas, instala las dependencias y ejecuta los servicios:

```bash
# API Gateway (Puerto 8080)
cd api-gateway
npm install
node server.js

# MS Notificaciones (Escucha RabbitMQ)
cd ms-notificaciones
npm install
node index.js
```
4. Iniciar la Interfaz de Usuario (React)
Finalmente, levanta el frontend para interactuar con el ecosistema a través del Gateway:

```bash
cd frontend-d1
npm install
npm start
```
La aplicación estará disponible en el navegador en http://localhost:3000.


🧪 Pruebas y Carga de Datos (Seeding)
Como las bases de datos en PostgreSQL inician vacías, debes inyectar los datos maestros a través del API Gateway antes de realizar la primera compra. Ejecuta estos comandos en tu terminal (PowerShell):

1. Crear Usuario:

```powershell
Invoke-RestMethod -Uri http://localhost:8080/api/usuarios -Method Post -ContentType "application/json" -Body '{"nombre":"Juan Carlos","email":"juan@correo.com"}'
```
2. Crear Producto en Inventario:

```powershell
Invoke-RestMethod -Uri http://localhost:8080/api/inventario -Method Post -ContentType "application/json" -Body '{"nombre":"Leche Entera Larga Vida","precio":3500.0,"stock":100}'
```
Una vez confirmada la inserción de estos datos, puedes procesar compras exitosamente desde la interfaz gráfica simulando la AutoCaja.