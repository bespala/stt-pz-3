# Practical lesson pz-MQTT

## Опис роботи

Практична робота присвячена розгортанню та налаштуванню MQTT-брокера.

Мета роботи:

- ознайомитися з принципами роботи MQTT-протоколу;
- розгорнути MQTT-брокер у Docker;
- виконати налаштування базової конфігурації;
- протестувати механізм Publish/Subscribe;
- перевірити доставку повідомлень з використанням QoS.

---

# Використані технології

- Ubuntu 22.04 LTS
- Docker
- Docker Compose
- Eclipse Mosquitto
- Git
- GitHub

---

# Структура проєкту

```text
stt-pz-3/
├── broker/
│   ├── mosquitto.conf
│   ├── docker-compose.yml
├── screenshots/
├── README.md
├── .gitignore
└── .editorconfig
```

---

# Теоретична частина

## Що таке MQTT?

MQTT (Message Queuing Telemetry Transport) — це легкий мережевий протокол обміну повідомленнями, який працює за моделлю Publisher/Subscriber.

Основне застосування:

- IoT системи
- сенсори
- smart devices
- distributed systems
- event-driven architecture

---

# Основні поняття MQTT

## Topic

Topic — це логічний канал для передачі повідомлень.

Приклад:

```text
test/lab
```

---

## Publish

Publish — відправка повідомлення в topic.

Publisher надсилає повідомлення брокеру.

---

## Subscribe

Subscribe — підписка на topic.

Subscriber отримує повідомлення, які надходять у topic.

---

## QoS (Quality of Service)

QoS визначає рівень гарантії доставки повідомлень.

### QoS 0

At most once

Повідомлення може бути втрачено.

---

### QoS 1

At least once

Повідомлення гарантовано доставляється хоча б один раз.

---

### QoS 2

Exactly once

Повідомлення гарантовано доставляється рівно один раз.

---

# Розгортання MQTT брокера

Для реалізації використано MQTT-брокер Eclipse Mosquitto.

Конфігурація знаходиться у:

```text
broker/mosquitto.conf
```

---

# Конфігурація брокера

Файл:

```text
broker/mosquitto.conf
```

Вміст:

```conf
listener 1883
allow_anonymous true

listener 9001
protocol websockets
```

Пояснення:

| Параметр | Опис |
|----------|------|
| listener 1883 | стандартний порт MQTT |
| allow_anonymous true | дозвіл на підключення без авторизації |
| listener 9001 | WebSocket порт |
| protocol websockets | підтримка WebSocket |

---

# Docker конфігурація

Файл:

```text
broker/docker-compose.yml
```

Вміст:

```yaml
version: "3.8"

services:
  mqtt-broker:
    image: eclipse-mosquitto:2
    container_name: mqtt-broker
    restart: unless-stopped
    ports:
      - "1883:1883"
      - "9001:9001"
    volumes:
      - ./mosquitto.conf:/mosquitto/config/mosquitto.conf
```

---

# Запуск проєкту

Перейти в папку:

```bash
cd broker
```

Запустити брокер:

```bash
sudo docker-compose up -d
```

---

# Перевірка контейнера

Перевірити статус:

```bash
sudo docker ps
```

Очікуваний результат:

```text
mqtt-broker Up
```

---

# Publish / Subscribe тестування

## Крок 1. Підписка на topic

Відкрити перший термінал:

```bash
sudo docker exec -it mqtt-broker mosquitto_sub -h localhost -t test/lab
```

Subscriber очікує повідомлення.

---

## Крок 2. Публікація повідомлення

Відкрити другий термінал:

```bash
sudo docker exec -it mqtt-broker mosquitto_pub -h localhost -t test/lab -m "Hello MQTT from lab"
```

---

## Очікуваний результат

У першому терміналі з’явиться:

```text
Hello MQTT from lab
```

---

# Тестування QoS

## Subscribe

```bash
sudo docker exec -it mqtt-broker mosquitto_sub -h localhost -t test/qos -q 1
```

---

## Publish

```bash
sudo docker exec -it mqtt-broker mosquitto_pub -h localhost -t test/qos -q 1 -m "QoS message"
```

---

## Очікуваний результат

```text
QoS message
```

---

# Логи контейнера

Перевірити логи:

```bash
sudo docker logs mqtt-broker
```

---

# Screenshots

У папці `screenshots` збережені:

1. Запущений Docker контейнер
2. Subscriber terminal
3. Publish результат
4. QoS test

---

# Висновок

У ході виконання лабораторної роботи було:

✅ Розгорнуто MQTT-брокер у Docker  
✅ Виконано базове налаштування Mosquitto  
✅ Перевірено механізм Publish/Subscribe  
✅ Протестовано QoS  
✅ Вивчено основні принципи роботи MQTT-протоколу  

Практична робота підтвердила працездатність брокера та правильність конфігурації.
