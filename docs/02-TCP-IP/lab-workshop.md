# 🧪 Лабораторная работа №1: TCP/IP Модель

## Цель: Изучить 4 уровня TCP/IP на практике!

---

## 📊 Уровень 1: Связь (Network Interface Layer) 🔌

### Теория:
Этот уровень объединяет физический и канальный уровни OSI. Отвечает за отправку данных по конкретному устройству!

### Практика:

#### Шаг 1: Проверь статус сетевого интерфейса

```bash
# Linux/Mac:
ip link show eth0

# Windows PowerShell:
Get-NetAdapter -Name "Ethernet" | Select-Object Name, Status, LinkSpeed
```

**Что должно быть:**
- `state UP` или `Status Up` — интерфейс активен! ✅
- Скорость соединения (100 Mbps, 1 Gbps и т.д.)

#### Шаг 2: Посмотри MAC-адрес сетевой карты

```bash
# Linux/Mac:
ip link show eth0 | grep link/ether

# Windows PowerShell:
Get-NetAdapter -Name "Ethernet" | Select-Object MacAddress
```

**Что должно быть:**
- `link/ether AA:BB:CC:DD:EE:FF` или `MacAddress AA-BB-CC-DD-EE-FF`
- Это уникальный номер твоей сетевой карты! 📮

#### Шаг 3: Проверь доступность сети (физический уровень)

```bash
# Linux/Mac:
ethtool eth0 | grep "Link detected"

# Windows PowerShell:
Get-NetAdapter -Name "Ethernet" | Select-Object LinkStatus
```

**Что должно быть:**
- `Link detected: yes` или `LinkStatus True` — кабель подключён! 🔌✅

---

## 📊 Уровень 2: Интернет (Internet Layer) 🌍

### Теория:
Отвечает за адресацию между сетями. Каждый пакет получает IP-адрес и маршрут!

### Практика:

#### Шаг 1: Посмотри свой IP-адрес

```bash
# Linux/Mac:
ip addr show eth0 | grep inet

# Windows PowerShell:
Get-NetIPAddress -AddressFamily IPv4 | Select-Object IPAddress, InterfaceAlias
```

**Что должно быть:**
- `inet 192.168.x.x` или `inet 10.x.x.x` — твой локальный IP! 🏠
- Это адрес внутри твоей домашней сети

#### Шаг 2: Посмотри публичный IP (адрес роутера в интернете)

```bash
# Linux/Mac/Windows:
curl ifconfig.me

# Или через Google:
ping -c 1 google.com && grep "from" /proc/net/route
```

**Что должно быть:**
- Публичный IP провайдера (например, `85.234.x.x`)
- Это адрес твоего роутера в интернете! 🌍

#### Шаг 3: Посмотри маршруты (как компьютер находит путь)

```bash
# Linux/Mac:
ip route show

# Windows PowerShell:
Get-NetRoute -AddressFamily IPv4 | Select-Object DestinationPrefix, NextHop
```

**Что должно быть:**
- `default via 192.168.1.1 dev eth0` — маршрут по умолчанию! 🌉
- Это говорит: "Все неизвестные адреса отправляй на роутер!"

#### Шаг 4: Проверь пинг (ICMP)

```bash
# Linux/Mac/Windows:
ping -c 4 google.com

# Или пинг других сайтов:
ping -c 4 youtube.com
ping -c 4 github.com
```

**Что должно быть:**
- `64 bytes from xxx.xxx.xxx.xxx: icmp_seq=1 ttl=115 time=20 ms`
- Если нет ответа — сайт недоступен или есть блокировка! 🚫

---

## 📊 Уровень 3: Транспорт (Transport Layer) 🚚

### Теория:
Отвечает за доставку данных между приложениями. Разбивает большие данные на пакеты!

### Практика:

#### Шаг 1: Посмотри открытые порты (слушающие соединения)

```bash
# Linux/Mac:
netstat -tulpn | grep LISTEN

# Windows PowerShell:
Get-NetTCPConnection -State Listen | Select-Object LocalAddress, LocalPort, OwningProcess
```

**Что должно быть:**
- Порт 22 — SSH (удалённый доступ)
- Порт 80 — HTTP (веб-сайты)
- Порт 443 — HTTPS (защищённые сайты)
- Порт 53 — DNS (перевод адресов в IP)

#### Шаг 2: Проверь соединение с сайтом (порт 443 = HTTPS)

```bash
# Linux/Mac/Windows:
telnet google.com 443

# Или через curl:
curl -v https://google.com | head -10
```

**Что должно быть:**
- `Connected to google.com` или `HTTP/2 200 OK`
- Если ошибка — порт заблокирован или сайт недоступен! 🚫

#### Шаг 3: Посмотри статистику TCP/UDP пакетов

```bash
# Linux/Mac:
cat /proc/net/tcp

# Windows PowerShell:
Get-NetUDPEndpoint | Select-Object LocalAddress, LocalPort
```

**Что должно быть:**
- Список активных соединений и портов
- Это как журнал доставки писем! 📮

#### Шаг 4: Проверь работу DNS (перевод доменов в IP)

```bash
# Linux/Mac/Windows:
nslookup google.com

# Или через dig:
dig google.com
```

**Что должно быть:**
- `google.com has address 142.250.x.x`
- Это значит, что DNS работает и переводит домен в IP! 🔍

---

## 📊 Уровень 4: Прикладной (Application Layer) 💻

### Теория:
Прямой доступ к приложениям. Всё, что ты используешь!

### Практика:

#### Шаг 1: Проверь работу браузера (HTTP/HTTPS)

```bash
# Linux/Mac/Windows:
curl -v https://google.com | head -20
```

**Что должно быть:**
- `HTTP/2 200 OK` или `HTTP/1.1 301 Moved Permanently`
- Это значит, что браузер успешно получил страницу! 🌐✅

#### Шаг 2: Проверь работу почтового клиента (SMTP)

```bash
# Linux/Mac/Windows:
telnet mail.google.com 25

# Или через curl:
curl -v smtp://mail.google.com:25 | head -10
```

**Что должно быть:**
- `220 Gmail SMTP ready` или `250 OK`
- Это значит, что почтовый сервер работает! 📧✅

#### Шаг 3: Проверь работу мессенджера (если есть CLI)

```bash
# Telegram CLI (если установлен):
telegram-cli --help

# WhatsApp Web (через браузер):
# Открой https://web.whatsapp.com и посмотри QR-код
```

**Что должно быть:**
- Мессенджер должен подключиться к серверу
- Это значит, что ты можешь отправлять сообщения! 💬✅

#### Шаг 4: Проверь работу FTP (передача файлов)

```bash
# Linux/Mac/Windows:
ftp ftp.example.com
# Или через curl:
curl -O https://example.com/file.zip
```

**Что должно быть:**
- `Connected to example.com` или файл скачан успешно
- Это значит, что передача файлов работает! 📥✅

---

## ✅ Итоговая проверка: Все уровни TCP/IP вместе

### Полный тест от уровня 1 до 4:

```bash
# Уровень 1: Связь (проверь кабель/Wi-Fi)
ip link show eth0 | grep "state UP"

# Уровень 2: Интернет (IP-адрес и маршруты)
ip addr show eth0 | grep inet
ip route show | grep default

# Уровень 3: Транспорт (порты)
netstat -tulpn | grep LISTEN

# Уровень 4: Прикладной (браузер)
curl -v https://google.com | head -5
```

### ✅ Если все команды работают — ты успешно прошёл лабораторную! 🎉🎊🏆

---

## 📝 Домашнее задание:

1. **Сделай скриншоты** всех команд и результатов
2. **Запиши в файл** `lab-results.md` свои наблюдения
3. **Попробуй найти проблемы**: отключи кабель, выключи Wi-Fi и посмотри, что будет! 🔌❌

---

## 🎉 Поздравляю! Ты закончил лабораторную работу по TCP/IP!

Теперь ты знаешь, как работает интернет на практике! Это фундамент для понимания более сложных тем: маршрутизация, безопасность, DNS и т.д.! 🚀

**Создано с помощью OpenClaw AI Assistant** 🤖  
**Последнее обновление:** 13 марта 2026
