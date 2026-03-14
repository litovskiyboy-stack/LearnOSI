# 🌐 VLAN (Virtual LAN) — Шпаргалка

## 🔢 Номера VLAN

| Диапазон | Назначение | Примеры |
|----------|------------|---------|
| **1** | Default VLAN | Не использовать для данных |
| **10-99** | Стандартные VLAN | HR, IT, GUEST |
| **100-399** | Расширенные VLAN | Специальные функции |
| **400-1005** | Расширенный диапазон | Для некоторых платформ |

---

## 🛠️ Быстрые команды (Cisco IOS)

### Создание VLAN:
```bash
vlan <номер>
 name <имя>
 description <описание>
```

### Конфигурация Access порта:
```bash
interface <интерфейс>
 switchport mode access
 switchport access vlan <номер>
 spanning-tree portfast  ! Для конечных устройств
 no shutdown
```

### Конфигурация Trunk порта:
```bash
interface <интерфейс>
 switchport mode trunk
 switchport trunk native vlan <номер>  ! ≠ 1 для безопасности
 switchport trunk allowed vlan <список>  ! Фильтрация VLAN
 no shutdown
```

### Проверка конфигурации:
```bash
show vlan brief              # Все VLAN и порты в них
show interfaces trunk        # Статус trunk-портов
show interfaces status       # Статус всех портов
show spanning-tree vlan <n>  # STP для конкретного VLAN
```

---

## 🔄 Типы портов

| Тип | Описание | Где используется |
|-----|----------|------------------|
| **Access** | Один VLAN, без тега | ПК, принтеры, телефоны |
| **Trunk** | Несколько VLAN, с тегом 802.1Q | Между свитчами, к роутеру |
| **Hybrid** | Комбинация Access/Trunk | Некоторые производители (Huawei, H3C) |

---

## 📊 Пример конфигурации для офиса:

```bash
! Создание VLAN
vlan 10
 name HR-DEPARTMENT
vlan 20
 name IT-DEPARTMENT
vlan 30
 name GUEST-WIFI
vlan 99
 name MANAGEMENT

! Access порты (конечные устройства)
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

interface GigabitEthernet0/2
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

interface GigabitEthernet0/3
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown

! Trunk порт к роутеру (безопасная конфигурация)
interface GigabitEthernet0/24
 switchport mode trunk
 switchport trunk native vlan 99  ! ≠ 1
 switchport trunk allowed vlan 10,20,30,99
 no shutdown

! L3 интерфейс для роутинга между VLAN
interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface Vlan20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

interface Vlan30
 ip address 192.168.30.1 255.255.255.0
 no shutdown
```

---

## 🔐 Безопасность VLAN

### Атаки и защита:

| Атака | Описание | Защита |
|-------|----------|--------|
| **VLAN Hopping** | Прыжок между VLAN | Отключить DTP, native VLAN ≠ 1 |
| **VLAN Leakage** | Утечка пакетов в другие VLAN | Правильная настройка allowed vlan |
| **VLAN Manipulation** | Изменение VID в пакете | Проверка целостности тегов на ingress |

### Безопасные настройки:
```bash
! Отключить DTP (Dynamic Trunking Protocol)
interface GigabitEthernet0/1-23
 switchport nonegotiate  ! На всех Access портах

! Безопасный trunk порт
interface GigabitEthernet0/24
 switchport mode trunk
 switchport trunk native vlan 99  ! ≠ 1
 switchport trunk allowed vlan 10,20,30,99  ! Только нужные VLAN
```

---

## 🧪 Диагностика проблем

### Проблема: Устройства не получают IP-адреса

**Диагностика:**
```bash
show ip dhcp binding          # Проверить DHCP leases
show ip interface vlan <n>    # Статус L3 интерфейса VLAN
show arp                      # ARP таблица свитча
```

### Проблема: Trunk порт не передаёт все VLAN

**Диагностика:**
```bash
show interfaces trunk | include Gi0/24  # Разрешённые VLAN
show interfaces trunk | include Native  # Native VLAN mismatch
show interfaces GigabitEthernet0/24 counters errors  # Ошибки на порту
```

### Проблема: STP блокирует порт

**Диагностика:**
```bash
show spanning-tree vlan <n>    # Статус STP для VLAN
show spanning-tree blockedports  # Заблокированные порты
show spanning-tree detail      # Детальная информация
```

---

## 🎯 Ключевые термины

| Термин | Определение |
|--------|-------------|
| **Broadcast Domain** | Домен широковещания — область broadcast-пакетов |
| **Access Port** | Порт для одного VLAN (конечные устройства) |
| **Trunk Port** | Порт для нескольких VLAN (между свитчами) |
| **Native VLAN** | VLAN без тега на trunk-порту (по умолчанию 1) |
| **Tagging (802.1Q)** | Добавление метки VLAN к пакету (4 байта) |
| **Untagged** | Пакет без VLAN-тега |
| **SVI** | Switch Virtual Interface — L3 интерфейс для VLAN |
| **Router-on-a-Stick** | Роутинг между VLAN через один физический интерфейс |

---

## ⚡ Быстрые команды (Cheat Sheet)

### Создание и управление:
```bash
vlan <номер>
 name <имя>
 description <описание>
```

### Конфигурация портов:
```bash
! Access порт
switchport mode access
switchport access vlan <номер>

! Trunk порт
switchport mode trunk
switchport trunk native vlan <номер>  ! ≠ 1
switchport trunk allowed vlan <список>
```

### Проверка:
```bash
show vlan brief              # Все VLAN и порты
show interfaces trunk        # Статус trunk-портов
show interfaces status       # Статус всех портов
show spanning-tree vlan <n>  # STP для VLAN
```

---

## 📊 Troubleshooting Checklist

### ✅ Проверь:
- [ ] Правильно ли назначены порты в VLAN?
- [ ] Trunk порт передаёт нужные VLAN?
- [ ] Native VLAN ≠ 1 на trunk-портах?
- [ ] DTP отключён на Access портах?
- [ ] STP не блокирует необходимые порты?

### ❌ Избегай:
- Использование VLAN 1 для данных
- Native VLAN = 1 на trunk-портах
- Включение DTP там, где он не нужен
- Передачу всех VLAN через один trunk без фильтрации

---

## 🎓 Быстрые примеры использования

### Домашняя сеть с гостевым Wi-Fi:
```bash
vlan 10
 name MAIN-NETWORK        ! Ноутбук, телефон
vlan 20
 name SMART-HOME          ! Камеры, датчики IoT
vlan 30
 name GUEST-WIFI          ! Гостевой доступ
```

### Офисная сеть:
```bash
vlan 10
 name HR-DEPARTMENT       ! Отдел кадров
vlan 20
 name IT-DEPARTMENT       ! IT отдел
vlan 30
 name FINANCE             ! Финансы (изолировано)
vlan 40
 name GUEST-WIFI          ! Гостевой доступ
```

---

**Создано с помощью OpenClaw AI Assistant** 🤖  
**Последнее обновление:** 14 марта 2026
