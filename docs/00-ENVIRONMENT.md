# 🐧 Окружение для обучения сетевым технологиям

## 🎯 Рекомендуемое окружение (Linux)

### Основные дистрибутивы:

#### **Debian 12+** (Bookworm и новее)
- Стабильная ОС для серверов
- Отличная документация
- Активное сообщество
- Идеально для Freepbx/Asterisk

```bash
# Проверка версии Debian
cat /etc/debian_version
lsb_release -a
```

#### **CentOS 7+** (или Rocky Linux/AlmaLinux)
- Предназначена для enterprise окружений
- Долгосрочная поддержка (LTS)
- Стандарт в корпоративном секторе
- Отлично подходит для серверов VoIP

```bash
# Проверка версии CentOS/RHEL
cat /etc/os-release
rpm -qa | grep kernel
```

### **Ubuntu LTS** (опционально)
- Пользовательская ОС с серверными версиями
- Хорошая документация и сообщество
- Подходит для обучения и тестирования

---

## 📦 Необходимые пакеты

### Базовый набор инструментов:

```bash
# Debian/Ubuntu
sudo apt update && sudo apt install -y \
    wireshark tcpdump net-tools iputils-ping dnsutils nmap \
    telnet curl wget git vim nano \
    freepbx asterisk \
    virtualbox-dkvm qemu-kvm

# CentOS/RHEL
sudo yum update && sudo yum install -y \
    wireshark tcpdump net-tools iputils-ping dnsutils nmap \
    telnet curl wget git vim nano \
    freepbx asterisk \
    virt-manager libvirt
```

### Расширенный набор:

```bash
# Мониторинг и логирование
sudo apt install -y prometheus grafana zabbix-agent \
    logrotate rsyslog syslog-ng

# Безопасность
sudo apt install -y fail2ban iptables nftables \
    openssl ca-certificates

# Веб-серверы и базы данных
sudo apt install -y nginx apache2 mysql-server postgresql \
    php-fpm redis memcached
```

---

## 🐳 Виртуализация

### VirtualBox (для тестирования)

```bash
# Установка на Debian/Ubuntu
wget https://download.virtualbox.org/virtualbox/7.0.16/virtualbox-7.0_7.0.16_focal.amd64.deb
sudo dpkg -i virtualbox-7.0_*.deb
sudo apt install -f

# Установка на CentOS/RHEL
sudo yum install -y VirtualBox
```

### KVM/QEMU (нативная виртуализация)

```bash
# Debian/Ubuntu
sudo apt install -y qemu-kvm libvirt-daemon-system virt-manager

# CentOS/RHEL
sudo yum install -y qemu-kvm libvirt-daemon-kvm virt-manager

# Запуск и управление
sudo systemctl enable --now libvirtd
virsh list
virt-manager
```

---

## 📞 Freepbx/Asterisk (VoIP серверы)

### Установка Freepbx на Debian 12:

```bash
# Добавление репозитория Freepbx
echo "deb http://packages.freepbx.org/debian bookworm main" | sudo tee /etc/apt/sources.list.d/freepbx.list

# Импорт GPG ключа
wget -qO - https://packages.freepbx.org/insecure.key | sudo apt-key add -

# Установка Freepbx
sudo apt update && sudo apt install freepbx
```

### Установка Asterisk (без Freepbx):

```bash
# Debian/Ubuntu
sudo apt install -y asterisk asterisk-mysql asterisk-voicemail-sofia

# CentOS/RHEL
sudo yum install -y asterisk asterisk-mysql asterisk-voicemail-sofia
```

---

## 🔧 Настройка сети (Linux)

### Проверка сетевого интерфейса:

```bash
# Посмотреть IP-адрес
ip addr show eth0

# Посмотреть маршруты
ip route show

# Проверить подключение к интернету
ping -c 4 google.com

# Посмотреть открытые порты
netstat -tulpn | grep LISTEN
```

### Настройка статического IP (Debian/Ubuntu):

```bash
# Редактирование файла интерфейса
sudo nano /etc/netplan/01-netcfg.yaml

# Пример конфигурации:
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      routes:
        - via 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]

# Применение изменений
sudo netplan apply
```

### Настройка статического IP (CentOS/RHEL):

```bash
# Редактирование файла интерфейса
sudo nano /etc/sysconfig/network-scripts/ifcfg-eth0

# Пример конфигурации:
DEVICE=eth0
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4

# Перезапуск сети
sudo systemctl restart network
```

---

## 📊 Мониторинг и диагностика

### Проверка нагрузки на систему:

```bash
# CPU, память, диск
top -bn1 | head -20

# Быстрая статистика
free -h
df -h
uptime
```

### Анализ сетевого трафика:

```bash
# Просмотр входящего/исходящего трафика в реальном времени
watch -n 1 'netstat -i'

# Анализ с помощью tcpdump
sudo tcpdump -i eth0 -nn -c 100

# Фильтрация по порту (например, HTTP)
sudo tcpdump -i eth0 port 80 -nn
```

### Проверка DNS:

```bash
# Проверка разрешения домена
nslookup google.com

# Или через dig
dig google.com +short
```

---

## 📚 Полезные ресурсы

- [Debian Wiki](https://wiki.debian.org/)
- [CentOS Documentation](https://access.redhat.com/documentation/ru-ru/red_hat_enterprise_linux)
- [Asterisk Documentation](https://www.asterisk.org/docs/)
- [Freepbx Community](https://community.freepbx.org/)

---

**Создано с помощью OpenClaw AI Assistant** 🤖  
**Последнее обновление:** 13 марта 2026
