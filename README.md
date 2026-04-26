# Event-Driven Lab

Arquitectura orientada a eventos con Spring Boot y RabbitMQ.

## Arquitectura

```
[Producer REST API] --> [RabbitMQ Exchange] --> [Queue] --> [Consumer]
     :8080                messages.exchange    messages.queue
```

| Servicio | Puerto | Función |
|----------|--------|---------|
| producer-service | 8080 | Recibe mensajes vía REST y los publica en RabbitMQ |
| consumer-service | — | Escucha la cola y procesa mensajes |
| rabbitmq | 5672 / 15672 | Broker de mensajes (UI en 15672) |

## Requisitos

- Java 17+
- Maven 3.8+
- Docker + Docker Compose

## Ejecutar localmente

```bash
# 1. Build JARs
cd producer-service && mvn package -DskipTests && cd ..
cd consumer-service && mvn package -DskipTests && cd ..

# 2. Levantar servicios
docker compose up -d

# 3. Enviar mensaje (esperar ~20s a que RabbitMQ esté listo)
curl -X POST "http://localhost:8080/api/messages/send?message=Hola"

# 4. Ver logs del consumidor
docker compose logs consumer

# 5. Detener
docker compose down
```

## RabbitMQ Management UI

URL: `http://localhost:15672`  
Usuario: `guest` / Contraseña: `guest`  
Cola: `messages.queue`

## Imágenes Docker Hub

- `lauraro/producer-service`
- `lauraro/consumer-service`

## Evidencias

### 1. Servicios corriendo

![docker compose ps](docker%20compose%20ps.png)

### 2. Mensaje enviado desde el productor

![producer response](producer%20response.png)

### 3. Mensaje recibido por el consumidor

![consumer logs](consumer%20logs.png)

### 4. RabbitMQ Management UI

![rabbitmq ui](rabbitmq%20ui.png)

### 5. Imágenes en Docker Hub

![docker hub](docker%20hub.png)

---

## Estructura

```
event-driven-lab/
├── docker-compose.yml
├── producer-service/
│   ├── Dockerfile
│   └── src/main/java/com/eci/arcn/producer_service/
│       ├── config/RabbitMQConfig.java   # Exchange, Queue, Binding
│       └── controller/MessageController.java  # POST /api/messages/send
└── consumer-service/
    ├── Dockerfile
    └── src/main/java/com/eci/arcn/consumer_service/
        ├── config/RabbitMQConfig.java   # Queue declaration
        └── listener/MessageListener.java  # @RabbitListener
```
