# 🌐 Networks Learning Platform

Интерактивная платформа для изучения сетевых технологий от начала до конца!

## 📚 Структура курса

### 1️⃣ [OSI Model](docs/01-OSI/)
- Теория и объяснение каждого уровня
- Жизненные примеры и аналогии
- Практические задания

### 2️⃣ [TCP/IP Stack](docs/02-TCP-IP/)
- Сравнение с OSI моделью
- IP-адресация, подсети, маршрутизация
- DNS, DHCP, NAT

### 3️⃣ [VLAN & Switching](docs/03-VLAN/)
- Виртуальные локальные сети
- STP, EtherChannel
- Конфигурация коммутаторов

### 4️⃣ [Routing Protocols](docs/04-Routing/)
- OSPF, EIGRP, BGP
- Маршрутизация в enterprise сетях
- Troubleshooting

### 5️⃣ [Network Security](docs/05-Security/)
- ACL, Firewall
- Безопасность маршрутов
- Best practices

## 🛠️ Инструменты для практики

- **Wireshark** - анализ трафика в реальном времени 🕵️‍♂️
- **Linux CLI** - bash скрипты, мониторинг и диагностика
- **VirtualBox/KVM** - виртуальные машины для тестирования
- **Freepbx/Asterisk** - настройка VoIP серверов
- **Debian 12+/CentOS 7+** - основные дистрибутивы для работы

## 🐧 Рекомендуемое окружение (Linux)

- **ОС**: Debian 12+, CentOS 7+, Ubuntu LTS
- **Серверы**: Freepbx/Asterisk, Nginx, Apache, MySQL
- **Мониторинг**: Prometheus, Grafana, Zabbix
- **Анализ трафика**: tcpdump, tshark (Wireshark CLI)

## 📦 Установка необходимых пакетов (Debian/Ubuntu)

```bash
# Установка инструментов для анализа сетей
sudo apt update
sudo apt install -y wireshark tcpdump net-tools iputils-ping dnsutils nmap

# Для CentOS/RHEL
sudo yum install -y wireshark tcpdump net-tools iputils dnsutils nmap
```

## 📦 Установка необходимых пакетов (CentOS 7+)

```bash
# Установка инструментов для анализа сетей
sudo yum update
sudo yum install -y wireshark tcpdump net-tools iputils-ping dnsutils nmap

# Для CentOS 7 (если пакеты не найдены)
sudo yum install -y NetworkManager-tcpdump
```

## 🐳 Виртуальные машины для тестирования

- **VirtualBox** - бесплатная виртуализация
- **KVM/QEMU** - нативная виртуализация Linux
- **Docker** - контейнеры для быстрой настройки окружений

---

**Примечание**: Этот курс ориентирован на работу с Linux, Freepbx и современными дистрибутивами (Debian 12+, CentOS 7+). Все примеры и лабораторные работы адаптированы под это окружение.

## 📖 Как использовать

1. Открой файл `.md` в [Obsidian](https://obsidian.md)
2. Читай теорию и примеры
3. Выполняй лабораторные работы в `labs/`
4. Проверяй себя через тесты в каждом разделе

## 🎯 Цели обучения

- Понимать, как работают сети на глубоком уровне
- Уметь конфигурировать оборудование Cisco/Juniper
- Решать реальные проблемы сетей
- Подготовиться к сертификациям (CCNA, JNCIA)

---

**Создано с помощью OpenClaw AI Assistant** 🤖  
**GitHub:** [litovskiyboy-stack/LearnOSI](https://github.com/litovskiyboy-stack/LearnOSI)
