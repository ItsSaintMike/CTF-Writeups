# 🎯 Darts — Reverse Engineering Challenge

> Решение задачи "Darts" с HackerLabs (легкий уровень)

---

## 📋 Описание задачи

| Параметр | Значение |
|----------|----------|
| **Название** | Darts |
| **Платформа** | HackerLabs |
| **Уровень** | Легкий |
| **Тип** | Reverse Engineering |
| **Файлы** | `darts.js`, `darts.js.deps`, `darts.js.map` |

---

## 🎯 Цель

Найти флаг, который проверяется программой `darts.js`.

Запуск программы:
```bash
node darts.js 'test'
```

🛠️ Инструменты
| Инструмент | Назначение |
|------------|------------|
| grep       | Поиск по тексту в файлах |
| sed        | Извлечение фрагментов кода |
| Node.js    | Запуск JavaScript-скриптов |
| JavaScript | Написание скрипта расшифровки |

📁 Структура файлов
~/Downloads/
├── darts.js          # Скомпилированный Dart-код
├── darts.js.deps     # Зависимости
└── darts.js.map      # Source map (для отладки)

🔍 Анализ кода
Первым делом ищем строковые сообщения в коде:

```bash
grep -E "Access|Denied|Granted|Usage|Invalid" darts.js -n
```

```Результат:
1102:  if(t.length<3){A.as("Usage: node darts.js <your_flag>")
1110:    A.as("!!! Access Granted! Flag is correct !!!")
1111:  else A.as("Access Denied! Invalid key.")
Вывод: Программа принимает аргумент командной строки и проверяет его.
```

## Точка входа
Смотрим контекст найденных сообщений:

```bash
sed -n '1095,1120p' darts.js
```

Функция d5() — точка входа:
```d5() {
    var t = v.G.process.argv
    if (t == null) {
        A.as("Error: process not found. Run this using Node.js")
        return
    }
    if (t.length < 3) {
        A.as("Usage: node darts.js <your_flag>")
        return
    }
    A.as("Okay, I agree that all of this looks terrible...")
    if (A.cT(A.bs(t[2])))  // ← функция проверки
        A.as("!!! Access Granted! Flag is correct !!!")
    else
        A.as("Access Denied! Invalid key.")
}
```

Функция проверки
Находим функцию cT():

```bash
sed -n '1115,1150p' darts.js
```

```javascript
cT(a) {
    var t, s, r, q, p = a.length
    
    // Проверка длины флага
    if (p !== 29) return !1
    
    // Закодированный флаг (массивы чисел)
    t = [20, 25, 19, 76, 21, 73, 134, 48, 21, 62, 48, 67, 61, 65, 82]
    s = [32, 26, 46, 25, 67, 48, 68, 66, 56, 52, 133, 48, 131, 65]
    
    // Формирование массива r (чередование t и s)
    r = A.ar([], u.t)
    for (q = 0; q < 15; ++q) {
        r.push(t[q])
        if (q < 14) r.push(s[q])
    }
    
    // Проверка каждого символа
    for (q = 0; q < p; ++q) {
        if ((a.charCodeAt(q) ^ 66) + 19 !== r[q]) return !1
    }
    return !0
}
```

💻 Скрипт для расшифровки
Создаем скрипт под названием solve.js
```javascript
// Исходные массивы из функции cT()
const t = [20, 25, 19, 76, 21, 73, 134, 48, 21, 62, 48, 67, 61, 65, 82];
const s = [32, 26, 46, 25, 67, 48, 68, 66, 56, 52, 133, 48, 131, 65];
// Формирование массива r (логика из cT())
const r = [];
for (let q = 0; q < 15; q++) {
    r.push(t[q]);
    if (q < 14) r.push(s[q]);
}
// Расшифровка
let flag = '';
for (let q = 0; q < r.length; q++) {
    const charCode = (r[q] - 19) ^ 66;
    flag += String.fromCharCode(charCode);
}
console.log('Flag:', flag);
console.log('Length:', flag.length);
```

🚀 Запуск
```bash
node solve.js
```

Результат:
Flag: CODEBY{D0rt_1s_m0gic_0r_h2ll}
Length: 29

✅ Проверка
```bash
node darts.js "CODEBY{D0rt_1s_m0gic_0r_h2ll}"
```

Вывод:
Okay, I agree that all of this looks terrible...
!!! Access Granted! Flag is correct !!!
