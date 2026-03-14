# 🌐 VLAN (Virtual LAN) — Виртуальные локальные сети

## 📌 Что такое VLAN?

**VLAN (Virtual Local Area Network)** — это технология, которая позволяет логически разделить одну физическую сеть на несколько независимых виртуальных сетей.

### 🔍 Простая аналогия:
Представь офисный этаж с одним большим открытым пространством (одна физическая сеть). VLAN — это как перегородки, которые делят это пространство на отдельные комнаты (HR отдел, IT отдел, Гостевой Wi-Fi), но при этом все комнаты находятся в одном здании.

---

## 🎯 Зачем нужны VLAN?

### 1. **Безопасность** 🔒
- Разделение чувствительных данных (финансы, HR) от общих ресурсов
- Ограничение доступа между отделами

### 2. **Организация** 📊
- Логическая группировка устройств по функциям, а не по физическому расположению
- Упрощение управления сетью

### 3. **Производительность** ⚡
- Снижение широковещательного трафика (broadcast traffic)
- Уменьшение коллизий в сегментах сети

---

## 🏗️ Как работает VLAN?

### Физическая сеть:
```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Switch A   │───▶│  Switch B   │───▶│  Router     │
│             │    │             │    │             │
│ PC HR       │    │ PC IT       │    │ Internet    │
└─────────────┘    └─────────────┘    └─────────────┘
```

### С VLAN:
```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Switch A   │    │  Switch B   │    │  Router     │
│             │    │             │    │             │
│ VLAN 10 HR  │    │ VLAN 20 IT  │    │ VLAN 30 GUEST│
└─────────────┘    └─────────────┘    └─────────────┘
```

---

## 🔢 Номера VLAN (ID)

| Номер | Назначение |
|-------|------------|
| **1** | Default VLAN (обычно не используется для данных) |
| **10-99** | Стандартные VLAN (стандарт IEEE 802.1Q) |
| **100-399** | Расширенные VLAN (Cisco proprietary) |
| **400-1005** | Расширенный диапазон (для некоторых платформ) |

### Примеры использования:
- **VLAN 10** — HR отдел
- **VLAN 20** — IT отдел  
- **VLAN 30** — Гостевой Wi-Fi
- **VLAN 40** — Финансы
- **VLAN 99** — Управление сетью (management)

---

## 🛠️ Команды для работы с VLAN (Cisco IOS)

### Создание VLAN:

```bash
# Войти в режим конфигурации
enable
configure terminal

# Создать VLAN с номером и именем
vlan 10
 name HR-DEPARTMENT

vlan 20
 name IT-DEPARTMENT

vlan 30
 name GUEST-WIFI

# Выйти из режима конфигурации
exit
write memory
```

### Назначение портов в VLAN:

```bash
# Войти в интерфейс порта
interface GigabitEthernet0/1

# Назначить порт в Access-режим (один VLAN)
switchport mode access
switchport access vlan 10

# Или в Trunk-режим (несколько VLAN)
switchport mode trunk
switchport trunk allowed vlan 10,20,30
```

### Проверка конфигурации:

```bash
# Посмотреть все VLAN на свитче
show vlan brief

# Посмотреть детали конкретной VLAN
show vlan id 10

# Посмотреть статус портов в VLAN
show interfaces status

# Посмотреть trunk-порты
show interfaces trunk
```

---

## 🔄 Типы портов в VLAN

### **Access Port** (доступный порт) 🔌
- Назначен только одному VLAN
- Используется для подключения конечных устройств (ПК, принтеры)
- Данные передаются без тега VLAN

### **Trunk Port** (стволовой порт) 🔄
- Может передавать несколько VLAN одновременно
- Используется между свитчами и роутерами
- Данные передаются с тегом VLAN (802.1Q)

### **Hybrid Port** (гибридный порт) ⚡
- Комбинация Access и Trunk
- Некоторые пакеты с тегами, некоторые без
- Используется в некоторых производителях свитчей (Huawei, H3C)

---

## 📊 Пример конфигурации для малого офиса:

```bash
! Создание VLAN
vlan 10
 name HR
vlan 20
 name IT
vlan 30
 name GUEST
vlan 99
 name MANAGEMENT

! Конфигурация свитча
interface GigabitEthernet0/1
 description PC-HR-01
 switchport mode access
 switchport access vlan 10

interface GigabitEthernet0/2
 description PC-IT-01
 switchport mode access
 switchport access vlan 20

interface GigabitEthernet0/3
 description GUEST-WIFI-AP
 switchport mode access
 switchport access vlan 30

! Trunk порт к роутеру
interface GigabitEthernet0/24
 description UPLINK-TO-ROUTER
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
```

---

## 🔐 Безопасность VLAN

### **VLAN Hopping Attack** (прыжок между VLAN) 🚨
Атака, когда злоумышленник получает доступ к другому VLAN.

#### Защита:
1. **Отключить PortFast на trunk-портах**
2. **Использовать native VLAN с номером ≠ 1**
3. **Настроить allowed vlan на trunk-портах**

```bash
# Безопасная конфигурация trunk порта
interface GigabitEthernet0/24
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
 no spanning-tree portfast
```

---

## 📝 Практическое задание:

### Создай VLAN для своего дома:

1. **VLAN 10** — Гости (гостевой Wi-Fi)
2. **VLAN 20** — Умный дом (камеры, датчики)
3. **VLAN 30** — Рабочие устройства (ноутбук, телефон)

---

## 🎓 Ключевые термины

| Термин | Определение |
|--------|-------------|
| **VLAN ID** | Уникальный номер VLAN (1-4094) |
| **Access Port** | Порт для одного VLAN (конечные устройства) |
| **Trunk Port** | Порт для нескольких VLAN (между свитчами) |
| **Native VLAN** | VLAN без тега на trunk-порту |
| **Tagging** | Добавление метки VLAN к пакету (802.1Q) |

---

## ⚡ Быстрые команды (Cheat Sheet)

### Создание VLAN:
```bash
vlan <номер>
 name <имя>
```

### Назначение порта в VLAN:
```bash
interface <интерфейс>
 switchport mode access
 switchport access vlan <номер>
```

### Проверка:
```bash
show vlan brief
show interfaces trunk
show interfaces status
```

---

**Создано с помощью OpenClaw AI Assistant** 🤖  
**Последнее обновление:** 14 марта 2026
