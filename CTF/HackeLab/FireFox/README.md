# 🦊 Firefox Forensics — Решение задачи "Firefox Profile"

> Решение задачи с HackerLabs (Forensics)

---

## 📋 Описание задачи

| Параметр | Значение |
|----------|----------|
| **Название** | Firefox Profile |
| **Платформа** | HackerLabs |
| **Уровень** | Средний |
| **Тип** | Forensics |
| **Файлы** | Профиль Firefox (дамп папки) |

---

## 🎯 Цель

Найти флаг, который спрятан в файлах профиля Firefox.

---

## 📁 Структура файлов
~/Downloads/hackerlab/firefox/
├── addons.json
├── cert19.db
├── compatibility.ini
├── containers.json
├── cookies.sqlite
├── cookies.sqlite-wal
├── crashes/
├── datareporting/
├── extension-presences.json
├── extensions.json
├── favicons.sqlite
├── favicons.sqlite-wal
├── firefox.zip
├── formhistory.sqlite
├── key4.db # 🔑 Ключи шифрования
├── logins.json # 🔐 Сохраненные пароли (зашифрованы)
├── login.json
├── minidumps/
├── permissions.sqlite
├── pkcs11.txt
├── places.sqlite # 📜 История, закладки
├── places.sqlite-wal
├── prefs.js # ⚙️ Настройки
├── protections.sqlite
├── search.json.mozl24
├── security_state/
├── serviceworker.txt
├── sessioncheckpoints.json
├── sessionstore-backups/ # 💾 Восстановленные сессии
├── SiteSecurityServiceState.bin
├── storage/
├── storage.sqlite
├── timeb.json
├── webappstore.sqlite
├── webappstore.sqlite-wal
└── xulstore.json

## 🔍 Анализ

### Шаг 1: Обнаружение ключевых файлов

Первым делом был найден файл `logins.json`, который содержит сохраненные пароли:

```bash
ls -la | grep -E "logins|key4"
```

Результат:
```
-rw-r--r-- 1 saintmike saintmike 2.1K Jul  4 12:00 logins.json
-rw-r--r-- 1 saintmike saintmike 8.0K Jul  4 12:00 key4.db
```

Вывод:
logins.json — содержит зашифрованные логины и пароли
key4.db — содержит ключи для расшифровки

## Шаг 2: Просмотр содержимого logins.json
```bash
cat logins.json
```

Фрагмент содержимого:
```
{
  "nextId": 4,
  "logins": [
    {
      "id": 1,
      "hostname": "https://hackerLab.pro",
      "encryptedUsername": "MEOEEPgSAAAAAAAAAAAAAAAAAAAEWFAYIKOZIhvcNAwcECA95fCnWALPRBCArwoWy6ipv5gWWCpygCOOjn/sLt9]JGuTOLWTngxkggAg一",
      "encryptedPassword": "MEoEEPgAAAAAAAAAAAAAAAAAAAEwFAYIKoZThyvcNAwCECCVbZCTLPeDCBCB6ChVrd9efcGZ1f8e6CLYTX/Onf4Dt5+KQLqjA1hE+kg="
    },
    {
      "id": 2,
      "hostname": "https://codeby.net",
      "encryptedUsername": "MDIEEPgAAAAAAAAAAAAAAAAAAAEWFAYIKoZIhvcNAwcECPW/gyceGXezBAiwtLKGfjV57w=",
      "encryptedPassword": "MDIEEPgAAAAAAAAAAAAAAAAAAAEwFAYIKoZThyvcNAwcECCShVHp0aQ4NBAhC8uLouFVDWA="
    },
    {
      "id": 3,
      "hostname": "https://codeby.school",
      "encryptedUsername": "MDoEEPgAAAAAAAAAAAAAAAAAAAEWFAYIKoZIhvcNAwcECFBubpWHTTetBBA7NC/AifiDV+/sLqCSMNJR",
      "encryptedPassword": "MDIEEPgAAAAAAAAAAAAAAAAAAAEwFAYIKoZThvcNAWcECHVShOLHFty7BAhSOE7Zx1qgxTQ=="
    }
  ]
}
```
Вывод:
Обнаружены 3 зашифрованных аккаунта
Для расшифровки нужны logins.json и key4.db

## Шаг 3: Использование firefox_decrypt
Для расшифровки паролей использована утилита firefox_decrypt:

```bash
# Клонирование репозитория
git clone https://github.com/unode/firefox_decrypt
cd firefox_decrypt
```

```
# Расшифровка паролей
python3 firefox_decrypt.py ~/Downloads/hackerlab/firefox/
```
Результат расшифровки:
```
Website:   https://hackerlab.pro
Username: 'for3ns1c_task@codeby.net'
Password: 'CODEBY{u_kn0w_what_d0_next}'

Website:   https://codeby.net
Username: '123'
Password: '123'

Website:   https://codeby.school
Username: 'test@mail.ru'
Password: 'test'
```
Вывод:
Пароль от hackerLab.pro оказался флагом!
```Флаг: CODEBY{u_kn0w_what_d0_next}```                
