# 🌐 Шпаргалка: 7 слой OSI — Application Layer (Прикладной уровень)

## 📌 Ключевые факты

| Параметр | Значение |
|----------|----------|
| **Номер слоя** | 7 |
| **Название** | Application Layer / Прикладной уровень |
| **Основная задача** | Интерфейс для приложений, доступ к сетям |
| **Аналогия** | Браузер, почтовая программа, мессенджер |

---

## 🎯 Основные функции

### 1. **Интерфейс для пользователей** 👤
- Прямое взаимодействие с пользователем
- Веб-браузеры, почтовые клиенты, мессенджеры

### 2. **Доступ к сетевым ресурсам** 🌍
- HTTP/HTTPS — веб-сайты
- SMTP/POP3/IMAP — электронная почта
- FTP/SFTP — передача файлов

### 3. **Протоколы и сервисы** 🔧
- DNS — разрешение доменов
- DHCP — автоматическая настройка IP
- Telnet/SSH — удалённый доступ

---

## 🌐 Ключевые протоколы (HTTP, SMTP, FTP, DNS)

| Протокол | Порт | Назначение | Безопасная версия |
|----------|------|------------|-------------------|
| **HTTP** | 80 | Передача веб-страниц | HTTPS (443) |
| **HTTPS** | 443 | Защищённый HTTP | — |
| **SMTP** | 25/587 | Отправка почты | SMTPS (465) |
| **POP3** | 110 | Получение почты | POP3S (995) |
| **IMAP** | 143 | Работа с почтой на сервере | IMAPS (993) |
| **FTP** | 20/21 | Передача файлов | SFTP (22), FTPS |
| **DNS** | 53 | Разрешение доменов | DNS over HTTPS (DoH) |
| **SSH** | 22 | Удалённый доступ | — |

---

## 🛠️ Команды для проверки (Linux)

### Проверка HTTP/HTTPS соединений:

```bash
# Простой запрос к веб-сайту
curl -I https://google.com

# Показать заголовки ответа
curl -vI https://google.com 2>&1 | head -20

# Получить содержимое страницы
curl https://google.com | head -50

# Проверить SSL-сертификат
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -text
```

### Проверка DNS:

```bash
# Разрешить домен в IP (nslookup)
nslookup google.com

# Или через dig (более подробный)
dig google.com +short

# Посмотреть все записи DNS для домена
host google.com

# Проверить MX-записи (почтовые серверы)
mx google.com
```

### Проверка SMTP/POP3/IMAP:

```bash
# Подключение к SMTP серверу (порт 25)
telnet mail.google.com 25

# Или через nc (netcat)
nc -vz smtp.gmail.com 587

# Проверка IMAP соединения
nc -vz imap.gmail.com 143
```

### Проверка SSH:

```bash
# Подключение к удалённому серверу
ssh user@remote-server.com

# Тестовое подключение без входа (только проверка порта)
ssh -o StrictHostKeyChecking=no user@host.com "echo connected && exit"

# Проверить, открыт ли порт 22
nc -vz ssh.example.com 22
```

### Проверка FTP/SFTP:

```bash
# Подключение к FTP серверу (анонимный доступ)
ftp ftp.example.com

# Или через sftp (безопасно)
sftp user@server.com

# Проверить порт SFTP
nc -vz sftp.example.com 22
```

---

## 📊 Сравнение протоколов передачи файлов

| Протокол | Порт | Безопасность | Использование |
|----------|------|--------------|---------------|
| **FTP** | 20/21 | ❌ Нет (текстовый) | Старые системы, тесты |
| **SFTP** | 22 | ✅ Да (SSH) | Современная передача файлов |
| **FTPS** | 989/990 | ✅ Да (TLS) | FTP с шифрованием |

---

## 🎯 Быстрые примеры использования

### HTTP запросы с curl:

```bash
# Простой GET-запрос
curl https://api.github.com/users/octocat

# Запрос с заголовками
curl -H "User-Agent: MyApp/1.0" https://example.com

# POST-запрос (отправка данных)
curl -X POST -d "name=John&age=30" https://api.example.com/users

# Файловый upload
curl -F "file=@document.pdf" https://upload.example.com/

# Проверка заголовков ответа
curl -I https://google.com 2>&1 | grep -i "server\|content-type"
```

### Работа с DNS:

```bash
# Получить IP-адрес домена
ping -c 4 google.com | grep PING

# Посмотреть все IP для домена (A, AAAA записи)
dig google.com +short

# Проверить MX-записи (почтовые серверы)
dig MX google.com +short
```

### SMTP тест:

```bash
# Подключение к почтовому серверу
telnet mail.example.com 25

# Интерактивная сессия:
HELO localhost
MAIL FROM:<sender@example.com>
RCPT TO:<recipient@example.com>
DATA
Subject: Test
This is a test message.
.
QUIT
```

---

## 🔍 Диагностика проблем

### Проблемы с HTTP/HTTPS:

```bash
# Проверить, открывается ли сайт
curl -I https://problematic-site.com 2>&1 | grep "HTTP"

# Если ошибка SSL-сертификата
echo | openssl s_client -connect problematic-site.com:443 2>&1 | grep -A5 "error\|verify"

# Проверить, блокируется ли порт фаерволом
nc -vz problematic-site.com 80
```

### Проблемы с DNS:

```bash
# Если сайт не открывается — проверь DNS
nslookup google.com

# Если нет ответа от DNS сервера
dig @8.8.8.8 google.com +short

# Проверить локальный DNS (обычно 127.0.0.53)
cat /etc/resolv.conf | grep nameserver
```

### Проблемы с SSH:

```bash
# Если не подключается по SSH
ssh -v user@host.com 2>&1 | grep "Connection refused\|timeout"

# Проверить, открыт ли порт 22
nc -vz host.com 22

# Посмотреть логи SSH сервера (если есть доступ)
sudo tail -f /var/log/auth.log
```

---

## 📊 Сравнение почтовых протоколов

| Протокол | Порт | Где хранится почта | Когда использовать |
|----------|------|-------------------|-------------------|
| **POP3** | 110/995 | На компьютере | Один компьютер, экономия места на сервере |
| **IMAP** | 143/993 | На сервере | Несколько устройств, синхронизация |

---

## 🎓 Ключевые термины

| Термин | Определение |
|--------|-------------|
| **HTTP** | HyperText Transfer Protocol — передача веб-страниц |
| **HTTPS** | HTTP + SSL/TLS шифрование |
| **DNS** | Domain Name System — перевод доменов в IP-адреса |
| **SMTP** | Simple Mail Transfer Protocol — отправка почты |
| **POP3/IMAP** | Протоколы получения электронной почты |
| **FTP/SFTP** | Протоколы передачи файлов |

---

## ⚡ Быстрые команды (Cheat Sheet)

### Веб-запросы:
```bash
# GET запрос с заголовками
curl -I https://example.com

# POST данные
curl -X POST -d "key=value" https://api.example.com/endpoint

# Загрузить файл
curl -F "file=@document.pdf" https://upload.example.com/
```

### DNS:
```bash
# IP домена
dig example.com +short

# MX записи (почта)
mx example.com

# Все записи DNS
nslookup -query=AXFR example.com
```

### Почта:
```bash
# Подключение к SMTP
telnet mail.example.com 587

# Проверка IMAP соединения
nc -vz imap.example.com 143
```

### Удалённый доступ:
```bash
# SSH подключение
ssh user@host.com

# SCP (копирование файлов через SSH)
scp file.txt user@host.com:/remote/path/

# SFTP интерактивная сессия
sftp user@host.com
```

---

## 🎯 Итоговая таблица

| Функция | Протокол | Пример использования |
|---------|----------|---------------------|
| **Веб-браузинг** | HTTP/HTTPS | Chrome, Firefox, Safari |
| **Электронная почта** | SMTP, POP3, IMAP | Gmail, Outlook, Thunderbird |
| **Передача файлов** | FTP, SFTP, SCP | Upload файлов на сервер |
| **Удалённый доступ** | SSH, Telnet | Управление сервером |
| **Разрешение доменов** | DNS | Перевод google.com в IP |

---

## 🔐 Безопасность (важно!)

### ❌ Избегай:
- Telnet (нешифрованный)
- FTP (пароли в открытом виде)
- HTTP (без шифрования)

### ✅ Используй:
- SSH вместо Telnet
- SFTP/FTPS вместо FTP
- HTTPS вместо HTTP
- IMAPS/POP3S для почты

---

**Создано с помощью OpenClaw AI Assistant** 🤖  
**Последнее обновление:** 14 марта 2026
