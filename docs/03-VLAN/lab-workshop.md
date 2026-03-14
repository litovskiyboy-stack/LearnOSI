# 🧪 Лабораторная работа: Настройка VLAN в домашней сети

## 🎯 Цель работы

Создать VLAN-сегментированную сеть для дома с разделением на:
1. **VLAN 10** — Основные устройства (ноутбук, телефон)
2. **VLAN 20** — Умный дом (камеры, датчики IoT)
3. **VLAN 30** — Гостевой Wi-Fi

---

## 📋 Требования к оборудованию

### Минимальное оборудование:
- L3 свитч (Cisco Catalyst 9300 или аналог)
- Роутер для выхода в интернет
- Несколько ПК/устройств для тестирования

### Альтернатива (без физического оборудования):
- Cisco Packet Tracer
- GNS3 с эмуляторами IOS
- EVE-NG для виртуальных лабораторий

---

## 🛠️ Шаг 1: Планирование сети

### Схема сети:

```
┌─────────────────────────────────────────────┐
│              L3 SWITCH (Core)               │
│                                             │
│  VLAN 10 ───[Access]── PC-HR-01            │
│  VLAN 20 ───[Access]── IoT-Camera          │
│  VLAN 30 ───[Access]── Guest-WiFi-AP      │
│                                             │
│  VLAN 99 ───[Access]── Management PC       │
│                                             │
│  [Trunk]───────────────▶ Router             │
│                              │               │
│                              ▼               │
│                        Internet (WAN)        │
└─────────────────────────────────────────────┘
```

### IP-адресация:

| VLAN | Назначение | Подсеть | Шлюз |
|------|------------|---------|------|
| 10 | Основные устройства | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Умный дом (IoT) | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Гостевой Wi-Fi | 192.168.30.0/24 | 192.168.30.1 |
| 99 | Управление свитчем | 192.168.99.0/24 | 192.168.99.1 |

---

## 📝 Шаг 2: Конфигурация L3 свитча

### 2.1 Создание VLAN:

```bash
! Войти в режим конфигурации
enable
configure terminal

! Создать VLAN с номером и именем
vlan 10
 name MAIN-NETWORK
 description Main Network Devices (Laptop, Phone)

vlan 20
 name SMART-HOME-IOT
 description IoT Devices (Cameras, Sensors)

vlan 30
 name GUEST-WIFI
 description Guest Wireless Network

vlan 99
 name MANAGEMENT
 description Switch Management VLAN

! Сохранить конфигурацию
write memory
```

### 2.2 Конфигурация Access портов:

```bash
! Порты для основных устройств (VLAN 10)
interface GigabitEthernet0/1
 description PC-HR-01 - Main Laptop
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

interface GigabitEthernet0/2
 description PHONE-01 - VoIP Phone
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

! Порты для умного дома (VLAN 20)
interface GigabitEthernet0/3
 description CAMERA-01 - Security Camera
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

interface GigabitEthernet0/4
 description SENSOR-01 - Motion Sensor
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

! Порт для гостевого Wi-Fi AP (VLAN 30)
interface GigabitEthernet0/5
 description GUEST-WIFI-AP
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown

! Management порт (VLAN 99)
interface GigabitEthernet0/24
 description MANAGEMENT-ACCESS
 switchport mode access
 switchport access vlan 99
 spanning-tree portfast
 no shutdown
```

### 2.3 Конфигурация Trunk порта к роутеру:

```bash
! Порт для связи с роутером (безопасная конфигурация)
interface GigabitEthernet0/25
 description UPLINK-TO-ROUTER
 switchport mode trunk
 switchport trunk native vlan 99  ! ≠ 1 для безопасности
 switchport trunk allowed vlan 10,20,30,99  ! Только нужные VLAN
 spanning-tree port type network
 no shutdown

! Отключить DTP на всех портах (безопасность)
interface range GigabitEthernet0/1-25
 switchport nonegotiate  ! Отключить Dynamic Trunking Protocol
```

### 2.4 Настройка L3 интерфейсов для роутинга:

```bash
! Создать SVI (Switch Virtual Interface) для каждого VLAN
interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface Vlan20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

interface Vlan30
 ip address 192.168.30.1 255.255.255.0
 no shutdown

! Interface для управления свитчем
interface Vlan99
 ip address 192.168.99.1 255.255.255.0
 no shutdown

! Проверить L3 интерфейсы
show ip interface brief
```

### 2.5 Настройка DHCP сервера (опционально):

```bash
! Включить DHCP сервис
ip dhcp pool MAIN-NETWORK
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8 8.8.4.4

ip dhcp pool SMART-HOME-IOT
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8 8.8.4.4

ip dhcp pool GUEST-WIFI
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8 8.8.4.4

! Проверить DHCP пулы
show ip dhcp pool
```

---

## 🛠️ Шаг 3: Конфигурация роутера (router-on-a-stick)

### 3.1 Основной интерфейс свитча:

```bash
interface GigabitEthernet0/0
 description UPLINK-TO-SWITCH
 no ip address
 duplex auto
 speed auto
 no shutdown
```

### 3.2 Subinterfaces для каждого VLAN:

```bash
! Subinterface для VLAN 10 (HR)
interface GigabitEthernet0/0.10
 description VLAN 10 - MAIN-NETWORK
 encapsulation dot1Q 10
 ip address 192.168.10.254 255.255.255.0

! Subinterface для VLAN 20 (IoT)
interface GigabitEthernet0/0.20
 description VLAN 20 - SMART-HOME-IOT
 encapsulation dot1Q 20
 ip address 192.168.20.254 255.255.255.0

! Subinterface для VLAN 30 (Guest)
interface GigabitEthernet0/0.30
 description VLAN 30 - GUEST-WIFI
 encapsulation dot1Q 30
 ip address 192.168.30.254 255.255.255.0

! Проверить subinterfaces
show ip interface brief
```

### 3.3 Настройка NAT для выхода в интернет:

```bash
! Определить внутренние сети (для NAT)
ip nat inside source list 1 interface GigabitEthernet0/1 overload

! Создать ACL для внутренних сетей
access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255
access-list 1 permit 192.168.30.0 0.0.0.255

! Настроить интерфейс WAN (интернет)
interface GigabitEthernet0/1
 description WAN-INTERNET
 ip address dhcp client  ! Или статический IP от провайдера
 ip nat outside
 no shutdown
```

---

## 🧪 Шаг 4: Тестирование и проверка

### 4.1 Проверка конфигурации свитча:

```bash
! Показать все VLAN и порты в них
show vlan brief

! Статус trunk-портов
show interfaces trunk

! Статус всех портов
show interfaces status

! Проверка L3 интерфейсов
show ip interface brief

! Проверка DHCP пулов (если настроен)
show ip dhcp pool

! Проверка ARP таблицы
show arp
```

### 4.2 Тестирование连通性:

#### С роутера (из subinterfaces):

```bash
# Ping шлюз каждого VLAN
ping 192.168.10.1    # VLAN 10 - должен пройти
ping 192.168.20.1    # VLAN 20 - должен пройти
ping 192.168.30.1    # VLAN 30 - должен пройти

# Ping management VLAN
ping 192.168.99.1    # VLAN 99 - должен пройти
```

#### С устройств в разных VLAN:

**С устройства в VLAN 10 (ноутбук):**
```bash
# Проверить получение IP-адреса
ipconfig /all  # Windows
ifconfig       # Linux/Mac

# Ping шлюз VLAN 10
ping 192.168.10.1    # Должен пройти

# Ping устройства в другом VLAN (должен пройти через роутер)
ping 192.168.20.100  # Устройство в VLAN 20 - должно пройти
```

**С устройства в VLAN 30 (гостевой):**
```bash
# Проверить изоляцию от основного VLAN
ping 192.168.10.1    # Должен пройти через роутер
ping 192.168.20.1    # Должен пройти через роутер

# Гостевые устройства не должны иметь доступ к внутренним ресурсам
# (настроить ACL на роутере для ограничения доступа)
```

### 4.3 Проверка безопасности VLAN:

```bash
! На свитче - проверить, что trunk порт передаёт только нужные VLAN
show interfaces GigabitEthernet0/25 switchport | include allowed

! Убедиться, что DTP отключён на всех портах
show dtp interface GigabitEthernet0/1-25

! Проверить native VLAN на trunk-портах
show interfaces trunk | include Native
```

---

## 🐛 Шаг 5: Troubleshooting (устранение неполадок)

### Проблема 1: Устройства не получают IP-адреса

**Диагностика:**
```bash
# На свитче - проверить DHCP пулы
show ip dhcp pool

# Проверить, что интерфейс VLAN активен
show ip interface vlan 10

# Посмотреть ARP таблицу
show arp
```

**Решение:**
- Убедиться, что DHCP сервер включён и настроен правильно
- Проверить, что интерфейс VLAN не заблокирован STP
- Проверить, что порт устройства в правильном VLAN

### Проблема 2: Trunk порт не передаёт все VLAN

**Диагностика:**
```bash
# Показать разрешённые VLAN на trunk-порту
show interfaces GigabitEthernet0/25 switchport | include allowed

# Проверить native VLAN mismatch
show interfaces trunk | include Native

# Посмотреть ошибки на порту
show interfaces GigabitEthernet0/25 counters errors
```

**Решение:**
- Добавить нужные VLAN в `switchport trunk allowed vlan`
- Убедиться, что native VLAN совпадает с обеих сторон
- Проверить физическую связь кабеля

### Проблема 3: STP блокирует порт

**Диагностика:**
```bash
# Показать статус spanning-tree для VLAN
show spanning-tree vlan 10

# Посмотреть заблокированные порты
show spanning-tree blockedports

# Проверить топологию
show spanning-tree detail
```

**Решение:**
- Убедиться, что порт не является redundant (резервным)
- Если нужно - отключить STP на этом порту (`spanning-tree portfast`)
- Проверить физическую топологию сети

### Проблема 4: Меж-VLAN роутинг не работает

**Диагностика:**
```bash
# На свитче - проверить L3 интерфейсы
show ip interface brief | include Vlan

# На роутере - проверить subinterfaces
show ip interface brief

# Проверить routing table на роутере
show ip route
```

**Решение:**
- Убедиться, что SVI интерфейсы настроены и активны
- Проверить, что subinterfaces на роутере имеют правильные VLAN tags
- Проверить routing table - должны быть маршруты ко всем подсетям

---

## 📊 Итоговая проверка

### ✅ Все должно работать:

1. **Устройства получают IP-адреса** из соответствующих DHCP пулов
2. **Меж-VLAN роутинг работает** — устройства в разных VLAN могут общаться через шлюз
3. **Broadcast изоляция** — broadcast пакеты не передаются между VLAN
4. **Безопасность** — гостевой VLAN изолирован от внутренних ресурсов

### 📝 Чек-лист проверки:

- [ ] Все VLAN созданы и активны (`show vlan brief`)
- [ ] Access порты назначены в правильные VLAN
- [ ] Trunk порт передаёт нужные VLAN (native ≠ 1)
- [ ] L3 интерфейсы SVI настроены и активны
- [ ] DHCP сервер работает (если используется)
- [ ] Меж-VLAN роутинг работает (ping между VLAN)
- [ ] Broadcast изоляция работает (broadcast пакеты не передаются между VLAN)

---

## 🎓 Дополнительные задачи (для продвинутых)

### Задача 1: Настройка Private VLAN (PVLAN)

Добавить изоляцию внутри VLAN 10, чтобы устройства в одном VLAN не могли общаться напрямую.

### Задача 2: ACL между VLAN

Ограничить доступ гостевого VLAN к внутренним ресурсам:

```bash
! На роутере - ограничить доступ гостевых устройств
access-list 100 deny ip 192.168.30.0 0.0.0.255 any log
access-list 100 permit ip 192.168.30.0 0.0.0.254 any

interface Vlan30
 ip access-group 100 in
```

### Задача 3: Настройка QoS для приоритизации трафика

Приоритезировать VoIP трафик над другими типами трафика.

---

## 📚 Полезные ссылки

- [Cisco VLAN Configuration Guide](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-2960-x-series/137458-vlanconf.html)
- [IEEE 802.1Q Standard](https://standards.ieee.org/standard/802_1q.html)
- [VLAN Troubleshooting Checklist](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-3560-series-switches/137459-vlantroubleshoot.html)

---

**Создано с помощью OpenClaw AI Assistant** 🤖  
**Последнее обновление:** 14 марта 2026
