# Practical lesson pz-MQTT

## Опис роботи

Розгортання та налаштування MQTT-брокера

Мета роботи:

Розгорнути MQTT-брокер (Mosquitto / EMQX / HiveMQ)

Налаштувати базову конфігурацію сервісу

Ознайомитись із принципами роботи MQTT-протоколу

Виконати публікацію та підписку на MQTT-топіки через Postman або інший MQTT-клієнт

Перевірити працездатність сервісу через інструменти тестування

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
<img width="982" height="100" alt="Screenshot 2026-05-06 015957" src="https://github.com/user-attachments/assets/58584d00-974d-4298-bc3b-e0edc13e971d" />

Як бачимо, наш контейнер піднявся

---

# Publish / Subscribe тестування

## Крок 1. Підписка на topic

Відкриваємо перший термінал та вписуємо наступну команду:

```bash
sudo docker exec -it mqtt-broker mosquitto_sub -h localhost -t test/lab
```

Subscriber очікує повідомлення.

---

## Крок 2. Публікація повідомлення

Відкриваємо другий термінал та вписуємо наступну команду:

```bash
sudo docker exec -it mqtt-broker mosquitto_pub -h localhost -t test/lab -m "Hello MQTT from lab"
```

---
<img width="957" height="217" alt="Screenshot 2026-05-06 020415" src="https://github.com/user-attachments/assets/6e67c915-6ade-4a07-8962-cf017e6f5cfe" />



Як бачимо на скріншоті, на першому терміналі з’явиться:: Hello MQTT from lab


---

## Крок 2. Тестування QoS

Відкриваємо перший термінал та вписуємо наступну команду: 

## Subscribe

```bash
sudo docker exec -it mqtt-broker mosquitto_sub -h localhost -t test/qos -q 1
```

---

Відкриваємо другий термінал та вписуємо наступну команду:

```bash
sudo docker exec -it mqtt-broker mosquitto_pub -h localhost -t test/qos -q 1 -m "QoS message"
```

---
<img width="952" height="212" alt="Screenshot 2026-05-06 020610" src="https://github.com/user-attachments/assets/85a735ec-790f-4513-9eec-b86b8900318c" />

бачимо повідомлення: QoS message


---



# Висновок

У ході виконання лабораторної роботи було:

 Розгорнуто MQTT-брокер у Docker  
 Виконано базове налаштування Mosquitto  
 Перевірено механізм Publish/Subscribe  
 Протестовано QoS  
 Вивчено основні принципи роботи MQTT-протоколу  

Практична робота підтвердила працездатність брокера та правильність конфігурації.
