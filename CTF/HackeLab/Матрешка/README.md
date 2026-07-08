# 🗜️ ZIP-матрешка (Multy-ZIP)

**Тип:** Форензика / Криптография  
**Сложность:** Легкая  
**Технологии:** `Python`, `ZIP`, `TAR`

---

## 📌 Описание

Дан архив `task.tar`, внутри которого находится вложенная цепочка ZIP-архивов. Каждый следующий архив защищен паролем, который равен **имени предыдущего архива** (без расширения `.zip`). В конце находится файл `flag.txt`.

---

## 🔍 Решение

### 1. Распаковка `task.tar`

```bash
tar -xf task.tar
```

### 2. Автоматическая распаковка цепочки
Скрипт перебирает все архивы flag_B2.zip → flag_C3.zip → ... → flag_Z26.zip, подставляя в качестве пароля имя предыдущего архива.

```solve.py:
python
import os
import zipfile
import tarfile

def extract_tar(name):
    with tarfile.open(name) as f:
        f.extractall()
        print(f"[+] {name} распакован")

def extract_zip(name, pwd):
    try:
        with zipfile.ZipFile(name) as f:
            f.extractall(pwd=pwd.encode())
            print(f"[+] {name} -> {pwd}")
            return True
    except:
        return False

# 1. Распаковка task.tar
extract_tar("task.tar")

# 2. Цепочка ZIP
for i in range(2, 27):
    cur = f'flag_{chr(64+i)}{i}.zip'
    pwd = "flag_A1" if i == 2 else f'flag_{chr(64+i-1)}{i-1}'

    if not extract_zip(cur, pwd):
        break
# 3. Чтение флага
with open("flag.txt") as f:
    print("\n🏁 Флаг:", f.read())
```

### 3. Запуск
```bash
python3 solve.py
```

🏁 Результат
```
[+] task.tar распакован
[+] flag_B2.zip -> flag_A1
[+] flag_C3.zip -> flag_B2
...
🏁 Флаг: CODEBY{...}
```

📎 Вывод
Задача решается простым скриптом-распаковщиком. Ключ — в понимании логики паролей (имя предыдущего архива).
