# 🎨 Шпаргалка: 6 слой OSI — Presentation Layer (Представленческий уровень)

## 📌 Ключевые факты

| Параметр | Значение |
|----------|----------|
| **Номер слоя** | 6 |
| **Название** | Presentation Layer / Представленческий уровень |
| **Основная задача** | Шифрование, сжатие, кодирование данных |
| **Аналогия** | Переводчик и редактор текста перед отправкой |

---

## 🔐 Основные функции

### 1. **Шифрование (Encryption)** 🔒
- Защита данных от перехвата
- Преобразование в нечитаемый формат
- Примеры: SSL/TLS, PGP, AES

### 2. **Сжатие данных (Compression)** 📦
- Уменьшение размера файлов
- Экономия полосы пропускания
- Примеры: JPEG, ZIP, GZIP

### 3. **Кодирование (Encoding)** 🔄
- Преобразование формата данных
- Совместимость между системами
- Примеры: ASCII, UTF-8, EBCDIC

---

## 🔑 Ключевые протоколы и форматы

| Протокол/Формат | Назначение | Использование |
|-----------------|------------|---------------|
| **SSL/TLS** | Шифрование соединений | HTTPS, SMTPS, IMAPS |
| **PGP/GPG** | Шифрование файлов/почты | Электронная почта |
| **JPEG/JPG** | Сжатие изображений | Фотографии, веб-сайты |
| **PNG** | Сжатие без потерь | Логотипы, графики |
| **GIF** | Анимация (до 256 цветов) | Простая анимация |
| **ZIP/GZIP** | Сжатие файлов | Архивы программ |
| **ASCII/UTF-8** | Кодировка текста | Веб-страницы, файлы |

---

## 🛠️ Команды для проверки (Linux)

### Проверка SSL/TLS сертификатов:

```bash
# Посмотреть детали сертификата сайта
openssl s_client -connect google.com:443 -showcerts

# Извлечь только сертификат
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -text

# Проверить дату истечения сертификата
openssl s_client -connect google.com:443 2>/dev/null | grep "notAfter"
```

### Проверка сжатия файлов:

```bash
# Сжать файл в ZIP
zip archive.zip file1.txt file2.txt

# Сжать в GZIP (создаёт .gz)
gzip largefile.txt

# Архивировать и сжать одновременно
tar -czvf archive.tar.gz folder/

# Распаковать архивы
unzip archive.zip
gunzip file.txt.gz
tar -xzvf archive.tar.gz
```

### Проверка кодировки файлов:

```bash
# Посмотреть кодировку файла
file --mime-encoding filename.txt

# Перевести в UTF-8 (если нужно)
iconv -f WINDOWS-1251 -t UTF-8 input.txt > output_utf8.txt

# Проверить кодировку через Python
python3 -c "import locale; print(locale.getpreferredencoding())"
```

---

## 🎯 Быстрые примеры использования

### Шифрование с помощью OpenSSL:

```bash
# Создать ключ шифрования
openssl genpkey -algorithm AES-256-CBC -out key.pem

# Зашифровать файл
openssl enc -aes-256-cbc -salt -in plaintext.txt -out encrypted.bin -pass pass:mypassword

# Расшифровать файл
openssl enc -d -aes-256-cbc -in encrypted.bin -out decrypted.txt -pass pass:mypassword
```

### HTTPS соединение (Presentation + Transport):

```bash
# Проверить SSL-соединение с заголовками
curl -vI https://google.com 2>&1 | grep "SSL"

# Посмотреть сертификат через curl
curl --cacert /etc/ssl/certs/ca-certificates.crt https://google.com
```

---

## 📊 Сравнение форматов изображений

| Формат | Сжатие | Цвета | Прозрачность | Использование |
|--------|--------|-------|--------------|---------------|
| **JPEG** | С потерями | 16.7 млн | ❌ Нет | Фотографии |
| **PNG** | Без потерь | 16.7 млн | ✅ Да | Логотипы, графики |
| **GIF** | Без потерь | До 256 | ✅ Да | Простая анимация |
| **WebP** | С потерями/без | 16.7 млн | ✅ Да | Современный веб |

---

## 🔍 Диагностика проблем

### Проблемы с SSL/TLS:

```bash
# Проверить, почему сайт не подключается по HTTPS
curl -v https://problematic-site.com 2>&1 | grep -i "ssl\|certificate"

# Посмотреть ошибки сертификата
openssl s_client -connect problematic-site.com:443 2>&1 | grep -A5 "error"
```

### Проблемы с кодировкой:

```bash
# Проверить, правильно ли открыт файл в редакторе
file filename.txt

# Если текст отображается как мусор — проблема с кодировкой
iconv -f UTF-8 -t WINDOWS-1251 input.txt > output.txt  # или наоборот
```

---

## 🎓 Ключевые термины

| Термин | Определение |
|--------|-------------|
| **Шифрование** | Преобразование данных в нечитаемый формат для защиты |
| **Сжатие** | Уменьшение размера файла без потери важной информации |
| **Кодирование** | Преобразование данных из одного формата в другой |
| **SSL/TLS** | Протоколы шифрования для безопасных соединений |
| **PGP/GPG** | Программы для шифрования электронной почты и файлов |

---

## ⚡ Быстрые команды (Cheat Sheet)

### Шифрование/дешифрование:
```bash
# AES-256 шифрование
openssl enc -aes-256-cbc -salt -in file.txt -out file.enc -pass pass:password

# Расшифровка
openssl enc -d -aes-256-cbc -in file.enc -out file.txt -pass pass:password
```

### Сжатие файлов:
```bash
# ZIP архив
zip -r archive.zip folder/

# GZIP сжатие
gzip largefile.txt

# TAR + GZIP
tar -czvf backup.tar.gz /path/to/backup/
```

### Проверка SSL:
```bash
# Детали сертификата
openssl s_client -connect site.com:443 -showcerts

# Срок действия сертификата
openssl x509 -in cert.pem -noout -dates
```

---

## 🎯 Итоговая таблица

| Функция | Протокол/Формат | Пример использования |
|---------|-----------------|---------------------|
| **Шифрование** | SSL/TLS, PGP, AES | HTTPS сайты, защищённая почта |
| **Сжатие изображений** | JPEG, PNG, WebP | Фотографии на сайте |
| **Сжатие файлов** | ZIP, GZIP, TAR | Архивы программ, бэкапы |
| **Кодировка текста** | UTF-8, ASCII, EBCDIC | Веб-страницы, текстовые файлы |

---

**Создано с помощью OpenClaw AI Assistant** 🤖  
**Последнее обновление:** 14 марта 2026
