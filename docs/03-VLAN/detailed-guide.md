# 🌐 VLAN (Virtual LAN) — Полное руководство

## 📌 Введение в VLAN

### Что такое VLAN?

**VLAN (Virtual Local Area Network)** — технология логического разделения физической сети на несколько независимых виртуальных сетей. Это позволяет создавать отдельные broadcast-домены без добавления физического оборудования.

---

## 🔍 Как работает VLAN технически?

### 802.1Q Tagging (стандарт IEEE)

Каждый Ethernet-фрейм имеет следующую структуру:

```
┌─────────┬─────────┬─────────┬─────────┬─────────┐
│ Preamble │ Dest MAC │ Src MAC │ Type    │ Data     │
└─────────┴─────────┴─────────┴─────────┴─────────┘
```

**С VLAN (802.1Q):**
```
┌─────────┬─────────┬─────────┬─────────┬─────────┬─────────┐
│ Preamble │ Dest MAC │ Src MAC │ 2 bytes │ VLAN ID │ Data     │
│          │         │         │ Tag     │ (16 bit) │          │
└─────────┴─────────┴─────────┴─────────┴─────────┴─────────┘
```

**Добавляется:** 4 байта между Src MAC и Type:
- **TPID (Tag Protocol Identifier):** `0x8100` — идентификатор VLAN-тега
- **TCI (Tag Control Information):** 16 бит с информацией о VLAN

### Поля TCI (Tag Control Information):

| Биты | Название | Описание |
|------|----------|----------|
| 3 bits | PCP (Priority Code Point) | Приоритет трафика (0-7) |
| 1 bit | DEI/Drop Eligible | Возможность сброса пакета |
| 12 bits | VID (VLAN Identifier) | Номер VLAN (0-4095) |

**Важно:** VLAN ID 0 и 4095 зарезервированы, usable диапазон: **1-4094**

---

## 🏗️ Архитектура VLAN

### Типы сетей с VLAN:

#### **Flat Network** (плоская сеть)
Все устройства в одном broadcast-домене. Неэффективно для больших сетей.

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Switch     │───▶│  PC HR      │    │  PC IT      │
│             │    │             │    │             │
│  PC Guest   │    │  PC Finance │    │  PC Sales   │
└─────────────┘    └─────────────┘    └─────────────┘
```

#### **VLAN Network** (сегментированная)
Каждый отдел — отдельный broadcast-домен.

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Switch     │    │  Switch     │    │  Switch     │
│             │    │             │    │             │
│ VLAN 10 HR  │    │ VLAN 20 IT  │    │ VLAN 30 GUEST│
└─────────────┘    └─────────────┘    └─────────────┘
```

---

## 🔄 Inter-VLAN Routing (межсетевое роутинг)

### Проблема:
Устройства в разных VLAN не могут общаться напрямую — это разные broadcast-домены.

### Решение: **Inter-VLAN Routing** через Layer 3 устройство (роутер или L3 свитч):

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Switch     │    │  Router/L3  │    │  Internet   │
│             │    │             │    │             │
│ VLAN 10 HR  │───▶│ Interface 0 │    │             │
│             │    │ (VLAN 10)   │    │             │
└─────────────┘    └─────────────┘    └─────────────┘

              VLAN 20 IT
              VLAN 30 GUEST
```

### Настройка роутинга между VLAN:

**Cisco IOS:**
```bash
! На свитче (Layer 3)
interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface Vlan20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

! Или на роутере с subinterfaces (router-on-a-stick)
interface GigabitEthernet0/0
 description UPLINK-TO-SWITCH
 no ip address
 duplex auto
 speed auto

interface GigabitEthernet0/0.10
 description VLAN 10 - HR
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface GigabitEthernet0/0.20
 description VLAN 20 - IT
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

---

## 🛠️ Конфигурация VLAN на разных платформах

### **Cisco IOS:**

#### Создание и управление VLAN:

```bash
! Войти в глобальный режим конфигурации
enable
configure terminal

! Создать VLAN с именем
vlan 10
 name HR-DEPARTMENT
 description Human Resources Department

vlan 20
 name IT-DEPARTMENT
 description Information Technology

vlan 30
 name GUEST-WIFI
 description Guest Wireless Network

! Сохранить конфигурацию
write memory
```

#### Конфигурация Access портов:

```bash
interface GigabitEthernet0/1
 description PC-HR-01
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

interface GigabitEthernet0/2
 description PC-IT-01
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown
```

#### Конфигурация Trunk портов:

```bash
interface GigabitEthernet0/24
 description UPLINK-TO-CORE-SWITCH
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
 spanning-tree port type access
 no shutdown
```

#### Проверка конфигурации:

```bash
! Показать все VLAN и порты в них
show vlan brief

! Детали конкретной VLAN
show vlan id 10

! Статус trunk-портов
show interfaces trunk

! Список всех портов по статусу
show interfaces status

! Проверка spanning-tree
show spanning-tree vlan 10
```

### **Linux (Open vSwitch):**

#### Установка:

```bash
# Debian/Ubuntu
sudo apt install openvswitch-switch

# RHEL/CentOS
sudo yum install openvswitch
```

#### Создание VLAN через OVS:

```bash
# Создать bridge с VLAN-поддержкой
ovs-vsctl add-br br0 \
  -- set Bridge br0 stp=disabled \
  -- set Interface br0 type=internal

# Добавить порт в VLAN 10
ovs-vsctl add-port br0 eth0 \
  -- set interface eth0 tag=10

# Добавить порт в VLAN 20
ovs-vsctl add-port br0 eth1 \
  -- set interface eth1 tag=20

# Показать конфигурацию
ovs-vsctl show
```

---

## 🔐 Безопасность VLAN

### **Атаки на VLAN:**

#### 1. **VLAN Hopping** (прыжок между VLAN) 🚨

**Методы атаки:**
- **Double Tagging Attack:** Отправка пакета с двумя тегами VLAN
- **Switch Spoofing:** Подключение rogue свитча в trunk-порт

**Защита от Double Tagging:**
```bash
! На свитче Cisco
interface GigabitEthernet0/1
 switchport nonegotiate  ! Отключить DTP (Dynamic Trunking Protocol)
 switchport trunk native vlan 99  ! Native VLAN ≠ 1
 switchport trunk allowed vlan 10,20,30
```

#### 2. **VLAN Leakage** (утечка VLAN) 🚨

Когда пакеты из одного VLAN "просачиваются" в другой через неправильную конфигурацию.

**Защита:**
- Правильная настройка allowed vlan на trunk-портах
- Отключение DTP на всех портах, кроме необходимых
- Использование private VLAN (PVLAN) для изоляции

#### 3. **VLAN Manipulation Attack** 🚨

Злоумышленник меняет VID в пакете для доступа к другим сегментам.

**Защита:**
- Проверка целостности VLAN-тегов на ingress портах
- Использование ACL (Access Control Lists) между VLAN

---

## 📊 Best Practices (лучшие практики)

### **1. Планирование VLAN:**

```
┌─────────────────────────────────────┐
│  VLAN Planning Guidelines           │
├─────────────────────────────────────┤
│  • Используйте стандартные номера   │
│    (10-99) для основных отделов     │
│  • Расширенные VLAN (100-399) для  │
│    специальных функций              │
│  • Избегайте VLAN ID 1 (default)   │
└─────────────────────────────────────┘
```

### **2. Назначение портов:**

```
┌─────────────────────────────────────┐
│  Port Assignment Rules              │
├─────────────────────────────────────┤
│  • Access порты для конечных       │
│    устройств (ПК, принтеры)         │
│  • Trunk порты между свитчами      │
│  • Минимальное количество VLAN     │
│    на одном trunk-порту            │
└─────────────────────────────────────┘
```

### **3. Безопасность:**

```
┌─────────────────────────────────────┐
│  Security Best Practices            │
├─────────────────────────────────────┤
│  • Отключить DTP на всех портах    │
│  • Использовать native VLAN ≠ 1    │
│  • Настроить allowed vlan списки   │
│  • Регулярно аудировать конфигурацию│
└─────────────────────────────────────┘
```

### **4. Spanning Tree Protocol (STP):**

```bash
! Оптимизация STP для VLAN
spanning-tree mode rapid-pvst  ! Rapid PVST вместо MSTP
spanning-tree extend system-id  ! Для многосегментных сетей
```

---

## 🧪 Лабораторная работа: Настройка VLAN в домашней сети

### **Цель:** Создать VLAN-сегментированную сеть для дома с гостевым Wi-Fi.

### **Требования:**
1. VLAN 10 — Основные устройства (ноутбук, телефон)
2. VLAN 20 — Умный дом (камеры, датчики)
3. VLAN 30 — Гостевой Wi-Fi
4. Inter-VLAN роутинг через L3 свитч

### **Оборудование:**
- L3 свитч (Cisco Catalyst 9300 или аналог)
- Роутер для выхода в интернет
- Несколько ПК/устройств

### **Шаги настройки:**

#### Шаг 1: Создание VLAN

```bash
enable
configure terminal

vlan 10
 name MAIN-NETWORK
 description Main Network Devices

vlan 20
 name SMART-HOME
 description IoT Devices (Cameras, Sensors)

vlan 30
 name GUEST-WIFI
 description Guest Wireless Network

vlan 99
 name MANAGEMENT
 description Switch Management VLAN

exit
write memory
```

#### Шаг 2: Конфигурация портов свитча

```bash
! Порты для основных устройств (VLAN 10)
interface GigabitEthernet0/1-4
 description MAIN-NETWORK-DEVICES
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

! Порты для умного дома (VLAN 20)
interface GigabitEthernet0/5-8
 description SMART-HOME-IOT
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

! Порт для гостевого Wi-Fi AP (VLAN 30)
interface GigabitEthernet0/9
 description GUEST-WIFI-AP
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown

! Trunk порт к роутеру
interface GigabitEthernet0/24
 description UPLINK-TO-ROUTER
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
 spanning-tree port type network
 no shutdown

! Management порт (VLAN 99)
interface GigabitEthernet0/25
 description MANAGEMENT-ACCESS
 switchport mode access
 switchport access vlan 99
 no shutdown
```

#### Шаг 3: Настройка L3 интерфейсов для роутинга

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
```

#### Шаг 4: Настройка роутера (router-on-a-stick)

```bash
! Основной интерфейс свитча
interface GigabitEthernet0/0
 description UPLINK-TO-SWITCH
 no ip address
 duplex auto
 speed auto

! Subinterfaces для каждого VLAN
interface GigabitEthernet0/0.10
 description VLAN 10 - HR
 encapsulation dot1Q 10
 ip address 192.168.10.254 255.255.255.0

interface GigabitEthernet0/0.20
 description VLAN 20 - IT
 encapsulation dot1Q 20
 ip address 192.168.20.254 255.255.255.0

interface GigabitEthernet0/0.30
 description VLAN 30 - GUEST
 encapsulation dot1Q 30
 ip address 192.168.30.254 255.255.255.0

! NAT для выхода в интернет (если нужно)
ip nat inside source list 1 interface GigabitEthernet0/1 overload
access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255
access-list 1 permit 192.168.30.0 0.0.0.255
```

#### Шаг 5: Проверка работы

```bash
# На свитче
show vlan brief
show interfaces trunk
show ip interface brief

# Тестирование连通性
ping 192.168.10.254  # От роутера к VLAN 10
ping 192.168.20.254  # От роутера к VLAN 20
ping 192.168.30.254  # От роутера к VLAN 30

# Тестирование меж-VLAN трафика (с разных устройств)
# С устройства в VLAN 10: ping 192.168.20.100
```

---

## 📊 Troubleshooting (устранение неполадок)

### **Проблема:** Устройства не получают IP-адреса

**Диагностика:**
```bash
# Проверить DHCP сервер
show ip dhcp binding

# Проверить интерфейс VLAN
show ip interface vlan 10

# Посмотреть ARP таблицу
show arp
```

### **Проблема:** Trunk порт не передаёт все VLAN

**Диагностика:**
```bash
# Показать разрешённые VLAN на trunk-порту
show interfaces trunk | include Gi0/24

# Проверить native VLAN mismatch
show interfaces trunk | include Native

# Посмотреть ошибки на порту
show interfaces GigabitEthernet0/24 counters errors
```

### **Проблема:** STP блокирует порт

**Диагностика:**
```bash
# Показать статус spanning-tree
show spanning-tree vlan 10

# Посмотреть заблокированные порты
show spanning-tree blockedports

# Проверить топологию
show spanning-tree detail
```

---

## 🎓 Ключевые термины (VLAN)

| Термин | Определение |
|--------|-------------|
| **Broadcast Domain** | Домен широковещания — область, где пакеты broadcast достигают всех устройств |
| **Access Port** | Порт для одного VLAN (конечные устройства) |
| **Trunk Port** | Порт для нескольких VLAN (между свитчами) |
| **Native VLAN** | VLAN без тега на trunk-порту (по умолчанию 1) |
| **Tagging** | Добавление метки VLAN к пакету (802.1Q) |
| **Untagged** | Пакет без VLAN-тега |
| **SVI (Switch Virtual Interface)** | Логический интерфейс для VLAN на L3 свитче |
| **Router-on-a-Stick** | Роутинг между VLAN через один физический интерфейс роутера с subinterfaces |

---

## ⚡ Быстрые команды (VLAN Cheat Sheet)

### Создание и управление:
```bash
vlan <номер>
 name <имя>
 description <описание>
```

### Конфигурация портов:
```bash
! Access порт
interface <интерфейс>
 switchport mode access
 switchport access vlan <номер>

! Trunk порт
switchport mode trunk
switchport trunk native vlan <номер>
switchport trunk allowed vlan <список>
```

### Проверка:
```bash
show vlan brief              # Все VLAN и порты
show interfaces trunk        # Статус trunk-портов
show interfaces status       # Статус всех портов
show spanning-tree vlan 10   # STP для VLAN 10
```

---

## 🎯 Итоговая таблица

| Функция | Команда/Протокол | Пример использования |
|---------|------------------|---------------------|
| **Создание VLAN** | `vlan <номер>` | `vlan 10` → HR отдел |
| **Access порт** | `switchport access vlan` | ПК в VLAN 10 |
| **Trunk порт** | `switchport mode trunk` | Связь между свитчами |
| **Inter-VLAN роутинг** | SVI или router-on-a-stick | Роутинг между отделами |
| **Безопасность** | ACL, Port Security | Ограничение доступа |

---

## 🔐 Рекомендации по безопасности VLAN

### ✅ DO (делать):
- Использовать native VLAN ≠ 1
- Отключить DTP на всех портах (`switchport nonegotiate`)
- Настроить allowed vlan списки на trunk-портах
- Регулярно аудировать конфигурацию VLAN
- Использовать private VLAN для изоляции внутри сегмента

### ❌ DON'T (не делать):
- Не использовать VLAN 1 для данных
- Не оставлять native VLAN по умолчанию
- Не включать DTP на портах, где он не нужен
- Не передавать все VLAN через один trunk без фильтрации

---

**Создано с помощью OpenClaw AI Assistant** 🤖  
**Последнее обновление:** 14 марта 2026
