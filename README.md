# RabbitMQ AMQP 1.0 Test Setup

This setup runs RabbitMQ in Docker with:

- Management UI enabled (`rabbitmq_management`)
- AMQP 1.0 plugin enabled (`rabbitmq_amqp1_0`)
- User `guest` / password `guest`

## Setup

### 1. Start RabbitMQ

```bash
docker-compose up
```

- Management UI: [http://localhost:15672](http://localhost:15672)
- AMQP: `localhost:5672`
- Login: `guest / guest`

### 2. RabbitMQ configuration

Use `rabbitmqadmin` (comes with management UI):

```bash
curl -O http://localhost:15672/cli/rabbitmqadmin
chmod +x rabbitmqadmin

# Set permissions
docker exec -it rabbitmq rabbitmqctl set_permissions -p / guest ".*" ".*" ".*"

# Create a queue
./rabbitmqadmin -u guest -p guest -V / declare queue name=my_queue durable=true

# Create a topic exchange
./rabbitmqadmin -u guest -p guest -V / declare exchange name=my_topic_exchange type=topic

# Bind queue to exchange
./rabbitmqadmin -u guest -p guest -V / declare binding source=my_topic_exchange destination=my_queue routing_key="user.*"

# Publish a message
./rabbitmqadmin -u guest -p guest -V / publish exchange=my_topic_exchange routing_key="user.created" payload='{"id":1,"name":"Elias"}'
```

## Debugging

### Verify plugins

```bash
docker exec -it rabbitmq rabbitmq-plugins list
```

- `rabbitmq_management` and `rabbitmq_amqp1_0` should show `[E*]` (enabled).

### Retrieve published messages

```bash
./rabbitmqadmin -u guest -p guest -V / get queue=my_queue
```

You should see the JSON payload you just published.

## Linking with n8n

<img width="543" height="568" alt="image" src="https://github.com/user-attachments/assets/40a00775-5650-45cc-8720-dcba255f9796" />

<img width="977" height="772" alt="image" src="https://github.com/user-attachments/assets/5b975675-e58f-490c-885b-566ed6b772db" />
