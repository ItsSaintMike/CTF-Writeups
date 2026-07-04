# 🐧 Решение задачи "ESP32 Forensics" через Wine

## 📋 Описание задачи

| Параметр | Значение |
|----------|----------|
| **Название** | ESP32 Forensics |
| **Платформа** | HackerLabs |
| **Уровень** | Средний |
| **Тип** | Forensics |
| **Файл** | `x<202e>gpj.exe` |

---

## 🎯 Цель

Найти флаг, который спрятан в программе `x<202e>gpj.exe`.
~/Downloads/hackerlab/flag/
└── x<202e>gpj.exe # Windows PE + ESP32 прошивка
---

## 🔍 Решение через Wine

### Шаг 1: Подготовка окружения

```bash
# Установка Wine (если не установлен)
sudo apt update
sudo apt install wine wine32 wine64
```

# Проверка версии
``` wine --version
# wine-9.0 (или подобное)
```

## 📁 Исходные данные
Шаг 2: Первичный анализ через strings
```bash
strings x<202e>gpj.exe | grep -i "flag\|setup\|rar"
```
Результат:
```
Rar!
CMT
SFX-
Setup=flag.jpg
Setup=prg.exe
TempMode
Silent=1
Overwrite=1
flag.jpg
prg.exe
chuvak.wav
Flag
When will you give me the flag?
C:\Users\Public\flag.txt
```

Вывод:
Это SFX-архив (RAR)
Содержит: flag.jpg, prg.exe, chuvak.wav
prg.exe работает с файлом C:\Users\Public\flag.txt

Шаг 3: Распаковка архива
```bash
# Переименовываем для корректного распознавания
mv 'x<202e>gpj.exe' archive.rar
```
```
# Распаковка через unrar
unrar x archive.rar
```
Извлеченные файлы:
```
flag.jpg
prg.exe
chuvak.wav
```

## Шаг 4: Проверка flag.jpg
```bash
# Проверка метаданных
exiftool flag.jpg
# Поиск строк
strings flag.jpg | grep -i "flag\|ctf"
```
# Проверка стеганографии
```
zsteg flag.jpg
binwalk flag.jpg
```
Результат: Флаг не найден в flag.jpg.

## Шаг 5: Проверка prg.exe
```bash
strings prg.exe | grep -i "flag\|users\|public"
```
Результат:
```
C:\Users\Public\flag.txt
When will you give me the flag?
Flag
```
Вывод: prg.exe создает или читает файл flag.txt в системной папке.

## Шаг 6: Запуск через Wine
# Запуск программы
```
bash
wine prg.exe
```
## Шаг 7: Получение флага
# Читаем созданный файл
```
cat ~/.wine/drive_c/users/Public/flag.txt
```
Вывод ```CODEBY{ChuV@k_fl@g_@nd_taSk}```
