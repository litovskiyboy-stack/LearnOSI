# 🧪 Лабораторная работа: Транспортный слой (TCP/UDP)

## 🎯 Цель работы

Изучить работу транспортного слоя, настроить серверы HTTP и DNS, проанализировать трафик с помощью Wireshark.

---

## 📋 Оборудование и ПО

### **Необходимое оборудование:**
- 2 компьютера (или виртуальные машины)
- Сетевая карта на каждом компьютере
- Подключены через коммутатор или напрямую

### **Необходимое ПО:**
- Linux (Ubuntu/Debian) или Windows
- Wireshark для анализа трафика
- Netcat (nc) для тестирования соединений
- Сервер Apache/Nginx (для HTTP)
- Сервер BIND (для DNS)

---

## 📝 Задание 1: Настройка HTTP сервера с TCP

### **Шаг 1. Установка веб-сервера**

#### **Ubuntu/Debian:**
```bash
# Установить Apache
sudo apt update
sudo apt install apache2 -y

# Проверить статус
sudo systemctl status apache2
```

#### **Windows Server:**
```powershell
# Включить роль IIS через PowerShell
Add-WindowsFeature Web-Server

# Или через GUI:
Server Manager → Manage → Add Roles and Features → Web Server (IIS)
```

### **Шаг 2. Проверка слушающего порта**

#### **Linux:**
```bash
# Показать все слушающие порты
sudo netstat -tlnp | grep :80

# Или:
sudo ss -tlnp | grep :80
```

Ожидаемый результат:
```
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1234/apache2
```

#### **Windows:**
```cmd
# Показать все слушающие порты
netstat -ano | findstr :80
```

Ожидаемый результат:
```
TCP    0.0.0.0:80           0.0.0.0:0              LISTENING       1234
```

### **Шаг 3. Тестирование соединения**

#### **С другого компьютера:**
```bash
# Простой тест с netcat (отправляет данные на порт 80)
echo "GET / HTTP/1.1\r\nHost: localhost\r\n\r\n" | nc <IP-сервера> 80
```

Ожидаемый результат — HTML страница Apache/Nginx.

---

## 📝 Задание 2: Настройка DNS сервера с UDP

### **Шаг 1. Установка BIND**

#### **Ubuntu/Debian:**
```bash
# Установить BIND9
sudo apt update
sudo apt install bind9 -y

# Проверить статус
sudo systemctl status bind9
```

#### **Windows Server:**
```powershell
# Включить роль DNS через PowerShell
Add-WindowsFeature DNS

# Или через GUI:
Server Manager → Manage → Add Roles and Features → DNS Server
```

### **Шаг 2. Настройка зоны**

Создайте файл зоны `/etc/bind/named.local` (Linux) или `forwarders.txt` (Windows):

#### **Пример зоны для локального домена:**
```bash
$TTL    86400
@       IN      SOA     ns1.example.com. admin.example.com. (
                        2026031401      ; Serial
                        3H               ; Refresh
                        1H               ; Retry
                        1W               ; Expire
                        1D               ; Minimum TTL
)

@       IN      NS      ns1.example.com.
ns1     IN      A       127.0.0.1

www     IN      A       192.168.1.100
mail    IN      A       192.168.1.101
ftp     IN      A       192.168.1.102
```

### **Шаг 3. Проверка слушающего порта**

#### **Linux:**
```bash
# DNS использует порт 53 (UDP)
sudo netstat -ulnp | grep :53

# Или:
sudo ss -ulnp | grep :53
```

Ожидаемый результат:
```
udp        0      0 127.0.0.1:53            0.0.0.0:*                           *   1234/named
```

#### **Windows:**
```cmd
# Показать все слушающие порты UDP
netstat -ano | findstr :53
```

Ожидаемый результат:
```
UDP    127.0.0.1:53        0.0.0.0:0              *           1234
```

### **Шаг 4. Тестирование DNS запросов**

#### **С другого компьютера:**
```bash
# Простой тест с nslookup
nslookup www.example.local <IP-сервера>

# Или:
dig @<IP-сервера> www.example.local
```

Ожидаемый результат — IP-адрес для www.example.local.

---

## 📝 Задание 3: Анализ трафика с помощью Wireshark

### **Шаг 1. Установка Wireshark**

#### **Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install wireshark -y
```

#### **Windows:**
Скачать с https://www.wireshark.org/download.html и установить.

### **Шаг 2. Захват трафика**

1. Откройте Wireshark
2. Выберите интерфейс сети (Ethernet/Wi-Fi)
3. Нажмите `Start` для начала захвата
4. В другом окне выполните тесты из заданий 1 и 2
5. Остановите захват кнопкой `Stop`

### **Шаг 3. Анализ TCP handshake**

#### **Найти SYN-запрос:**
1. Отфильтровать по протоколу: `tcp.flags.syn==1 && tcp.flags.ack==0`
2. Найдите пакет с SYN от вашего компьютера к порту 80
3. Обратите внимание на:
   - Source IP и порт (например, 192.168.1.50:54321)
   - Destination IP и порт (сервер:80)

#### **Найти SYN+ACK:**
1. Отфильтровать по протоколу: `tcp.flags.syn==1 && tcp.flags.ack==1`
2. Найдите ответ от сервера с SYN+ACK
3. Обратите внимание на Sequence Number и Acknowledgment Number

#### **Найти ACK:**
1. Отфильтровать по протоколу: `tcp.flags.syn==0 && tcp.flags.ack==1`
2. Найдите финальный ACK, завершающий handshake

### **Шаг 4. Анализ UDP запросов**

#### **Найти DNS запросы:**
1. Отфильтровать по протоколу: `udp.port == 53`
2. Найдите пакеты с DNS запросами (Query) и ответами (Response)
3. Обратите внимание на:
   - Source/Destination IP и порт
   - Query name (доменное имя)
   - Response code

### **Шаг 5. Сравнение TCP и UDP**

#### **Создайте таблицу:**

| Характеристика | TCP (HTTP) | UDP (DNS) |
|----------------|------------|-----------|
| **Handshake** | Есть (3 пакета) | Нет |
| **Порты** | 80 (TCP) | 53 (UDP) |
| **Надёжность** | Гарантируется | Без гарантий |
| **Заголовки** | Большие (20+ байт) | Маленькие (8 байт) |

---

## 📝 Задание 4: Тестирование с netcat

### **Шаг 1. Установка netcat**

#### **Linux:**
```bash
# Установить netcat-openbsd
sudo apt install netcat-openbsd -y
```

#### **Windows:**
Скачать с https://nmap.org/ncat/ и установить.

### **Шаг 2. Тестирование TCP соединения**

```bash
# Простое соединение (не отправляет данные)
nc <IP-сервера> 80

# Отправка данных
echo "GET / HTTP/1.1\r\nHost: localhost\r\n\r\n" | nc <IP-сервера> 80

# С таймаутом (5 секунд)
timeout 5 nc <IP-сервера> 80
```

### **Шаг 3. Тестирование UDP соединения**

```bash
# Отправка данных через UDP
echo "test" | nc -u <IP-сервера> 53

# С таймаутом
timeout 5 nc -u <IP-сервера> 53
```

---

## 📝 Задание 5: Настройка firewall для портов

### **Шаг 1. Настройка iptables (Linux)**

#### **Разрешить HTTP (TCP порт 80):**
```bash
# Разрешить входящие TCP соединения на порт 80
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Разрешить исходящие TCP соединения на порт 80
sudo iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT

# Сохранить правила (Ubuntu/Debian):
sudo netfilter-persistent save
```

#### **Разрешить DNS (UDP порт 53):**
```bash
# Разрешить входящие UDP соединения на порт 53
sudo iptables -A INPUT -p udp --dport 53 -j ACCEPT

# Сохранить правила:
sudo netfilter-persistent save
```

#### **Проверить правила:**
```bash
# Показать все правила
sudo iptables -L -n -v

# Или (современнее):
sudo iptables-save | grep -E "ACCEPT|DROP"
```

### **Шаг 2. Настройка ufw (упрощённый firewall)**

#### **Разрешить HTTP и HTTPS:**
```bash
# Разрешить SSH (порт 22)
sudo ufw allow 'OpenSSH'

# Разрешить Nginx/Apache (HTTP/HTTPS)
sudo ufw allow 'Nginx Full'

# Или вручную:
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Активировать firewall:
sudo ufw enable
```

#### **Проверить правила:**
```bash
# Показать все правила
sudo ufw status verbose

# Показать статистику:
sudo ufw status numbered
```

### **Шаг 3. Настройка Windows Firewall**

#### **Разрешить HTTP (порт 80):**
1. Панель управления → Система и безопасность → Брандмауэр Защитника Windows
2. Изменить параметры брандмауэра
3. Разрешить подключение для "HTTP" в профилях Private/Public

#### **Разрешить DNS (порт 53):**
1. Панель управления → Система и безопасность → Брандмауэр Защитника Windows
2. Изменить параметры брандмауэра
3. Разрешить подключение для "DNS" в профилях Private/Public

---

## 📝 Задание 6: Тестирование сценариев

### **Сценарий 1: Веб-сервер без firewall**

```bash
# На сервере — Apache работает, но нет правил iptables/ufw
# Попробовать подключиться с другого компьютера:
curl http://<IP-сервера>

# Ожидаемый результат — соединение отклонено (Connection refused)
```

### **Сценарий 2: Веб-сервер с разрешённым портом**

```bash
# После настройки правил iptables/ufw для порта 80:
curl http://<IP-сервера>

# Ожидаемый результат — HTML страница Apache/Nginx
```

### **Сценарий 3: DNS сервер без firewall**

```bash
# На сервере — BIND работает, но нет правил iptables/ufw
# Попробовать подключиться с другого компьютера:
nslookup www.example.local <IP-сервера>

# Ожидаемый результат — ответ от DNS сервера
```

### **Сценарий 4: DNS сервер с разрешённым портом**

```bash
# После настройки правил iptables/ufw для порта 53:
nslookup www.example.local <IP-сервера>

# Ожидаемый результат — ответ от DNS сервера
```

---

## 📝 Задание 7: Анализ проблем и решений

### **Проблема 1: TCP handshake не завершается**

#### **Симптомы:**
- Wireshark показывает SYN, но нет ответа SYN+ACK
- Или есть SYN+ACK, но нет финального ACK

#### **Возможные причины:**
1. Firewall блокирует порт
2. Сервер не слушает на нужном порту
3. Проблемы с сетевым соединением

#### **Решение:**
```bash
# Проверить слушающие порты:
sudo netstat -tlnp | grep :80

# Проверить firewall:
sudo ufw status verbose

# Проверить логи сервера:
sudo tail -f /var/log/apache2/error.log
```

### **Проблема 2: DNS запросы не получают ответа**

#### **Симптомы:**
- nslookup возвращает "Non-existent domain" или тайм-аут
- Wireshark показывает UDP пакеты, но нет ответов

#### **Возможные причины:**
1. Firewall блокирует порт 53
2. BIND не настроен правильно
3. Проблемы с зоной DNS

#### **Решение:**
```bash
# Проверить слушающие порты:
sudo netstat -ulnp | grep :53

# Проверить конфигурацию зоны:
sudo named-checkzone example.local /etc/bind/named.local

# Перезапустить BIND:
sudo systemctl restart bind9
```

### **Проблема 3: Высокий уровень потерь пакетов**

#### **Симптомы:**
- TCP retransmissions в Wireshark
- Медленная загрузка страниц
- Разрывы соединения

#### **Возможные причины:**
1. Проблемы с сетевым кабелем/Wi-Fi
2. Перегруженный коммутатор/маршрутизатор
3. Конфликт IP-адресов

#### **Решение:**
```bash
# Проверить качество соединения:
ping -c 10 <IP-сервера>

# Проверить на наличие коллизий (Ethernet):
sudo ethtool eth0 | grep -i collisions

# Проверить ARP таблицу:
arp -a
```

---

## 📝 Задание 8: Практическое задание — создание собственного HTTP сервера

### **Шаг 1. Написание простого HTTP сервера на Python**

Создайте файл `simple_http_server.py`:

```python
#!/usr/bin/env python3
import socket

HOST = '0.0.0.0'
PORT = 80

def handle_client(client_socket):
    request = client_socket.recv(1024).decode('utf-8')
    
    response = """HTTP/1.1 200 OK\r\n
Content-Type: text/html; charset=utf-8\r\n
Connection: close\r\n
\r\n
<html>
<head><title>Hello World</title></head>
<body>
<h1>Welcome to my HTTP server!</h1>
<p>This is a simple Python HTTP server.</p>
</body>
</html>
"""
    
    client_socket.sendall(response.encode('utf-8'))
    client_socket.close()

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT))
    s.listen(1)
    print(f"Server listening on {HOST}:{PORT}")
    
    while True:
        conn, addr = s.accept()
        with conn:
            handle_client(conn)

if __name__ == "__main__":
    handle_client(None)  # Для запуска без клиента
```

### **Шаг 2. Запуск сервера**

```bash
# Сделать файл исполняемым
chmod +x simple_http_server.py

# Запустить в отдельном терминале:
python3 simple_http_server.py &
```

### **Шаг 3. Тестирование**

```bash
# С другого компьютера:
curl http://<IP-сервера>

# Или с netcat:
echo "GET / HTTP/1.1\r\nHost: localhost\r\n\r\n" | nc <IP-сервера> 80
```

---

## 📝 Задание 9: Практическое задание — создание собственного DNS сервера

### **Шаг 1. Написание простого DNS сервера на Python**

Создайте файл `simple_dns_server.py`:

```python
#!/usr/bin/env python3
import socket

HOST = '0.0.0.0'
PORT = 53

# Простая зона для тестирования
ZONES = {
    'example.local': {
        'www': '192.168.1.100',
        'mail': '192.168.1.101',
        'ftp': '192.168.1.102'
    }
}

def handle_client(client_socket):
    try:
        # Получаем запрос (UDP)
        data, addr = client_socket.recvfrom(512)
        
        # Парсим DNS запрос (упрощённо)
        query_type = data[0]  # QTYPE
        
        if query_type == 1:  # A record
            # Простая логика для тестирования
            response = create_dns_response(data, 'www.example.local', '192.168.1.100')
            client_socket.sendto(response, addr)
    except Exception as e:
        print(f"Error handling client {addr}: {e}")

def create_dns_response(request_data, domain_name, ip_address):
    # Создаём DNS ответ (упрощённо)
    response = bytearray()
    
    # Header
    response.extend([0x81, 0x80])  # Flags: recursion desired
    
    # Question section (копия запроса)
    response.extend(request_data[:46])
    
    # Answer section
    response.extend([0xC0, 0x0C])  # Pointer to domain name
    response.extend([0x00, 0x01])  # Type: A record
    response.extend([0x00, 0x01])  # Class: IN
    
    # IP address
    ip_bytes = bytes(map(int, ip_address.split('.')))
    response.extend(ip_bytes)
    
    return bytes(response)

if __name__ == "__main__":
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
        s.bind((HOST, PORT))
        print(f"DNS server listening on {HOST}:{PORT}")
        
        while True:
            handle_client(s)
```

### **Шаг 2. Запуск сервера**

```bash
# Сделать файл исполняемым
chmod +x simple_dns_server.py

# Запустить в отдельном терминале:
python3 simple_dns_server.py &
```

### **Шаг 3. Тестирование**

```bash
# С другого компьютера:
nslookup www.example.local <IP-сервера>

# Или с dig:
dig @<IP-сервера> www.example.local
```

---

## 📝 Итоговый чек-лист

### **Проверка TCP (HTTP):**
- [ ] Сервер слушает на порту 80 (`netstat -tlnp | grep :80`)
- [ ] TCP handshake завершается успешно (Wireshark)
- [ ] HTTP запросы возвращают правильные ответы
- [ ] Firewall разрешает порт 80

### **Проверка UDP (DNS):**
- [ ] Сервер слушает на порту 53 (`netstat -ulnp | grep :53`)
- [ ] DNS запросы получают ответы (nslookup/dig)
- [ ] UDP пакеты передаются без handshake
- [ ] Firewall разрешает порт 53

### **Безопасность:**
- [ ] Только необходимые порты открыты в firewall
- [ ] Серверы работают только на нужных интерфейсах
- [ ] Логирование настроено и работает

---

## 📚 Полезные ссылки

- [Wireshark — Анализ TCP/UDP трафика](https://www.wireshark.org/)
- [Python socket documentation](https://docs.python.org/3/library/socket.html)
- [BIND9 Documentation](https://bind9.nlnetlabs.nl/doc/bind9.10.0/)

---

**Создано с помощью OpenClaw AI Assistant** 🤖  
**Последнее обновление:** 14 марта 2026
