# Oyster Programming Language

![Version](https://img.shields.io/badge/version-0.1.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)

Oyster is a dynamic, Perl-inspired scripting language with a focus on simplicity and performance.

## Features

- Dynamic typing with 16-byte tagged values
- Perl-inspired syntax with modern enhancements
- Fast VM with dispatch table (ASCII → handler)
- Module system with .osm (source) and .oem (compiled)
- Rich standard library (regex, io, net, math, os, json)
- Built-in debugger with breakpoints and stepping
- UTF-32 strings with O(1) indexed access

## Installation

`git clone https://github.com/denrav2019/Oyster-v0.2.0.git`  
`cd Oyster-v0.2.0`  
`make`  
`sudo make install`

## Quick Start

Create a file `hello.osf`:

`print("Hello, Oyster!")`

Then run:

`oyster compile hello.osf`  
`oyster run hello.oce`

## Example

`use "io" as io`  
`use "math" as m`  
`@content = io.slurp("data.txt")`  
`print("File size: " . len(@content))`  
`$result = m.sin(m.%PI / 2)`  
`print("sin(PI/2) = " . $result)`

## Building from Source

`make`  
`make test`  
`make install`  
`make clean`

## Dependencies

**Ubuntu/Debian:**  
`sudo apt install build-essential libpcre2-dev`

**Fedora/RHEL:**  
`sudo dnf install gcc make pcre2-devel`

**macOS:**  
`brew install pcre2`

## Author

**Daniil Kranchev**  
GitHub: [@denrav2019](https://github.com/denrav2019)  
Email: nnikus2017@gmail.com

## License

MIT License - see the LICENSE file for details.

## Acknowledgments

- Perl - for inspiration  
- Lua - for VM design concepts  
- The open source community


# Oyster Language v0.3.6

Oyster — минималистичный язык программирования, вдохновлённый Perl. Умеренная минималистичность, отсутствие системных переменных, единый числовой тип с фиксированной точкой, регистронезависимый синтаксис.

Девиз Oyster - эффективность, читаемость, минимализм!

### Быстрый старт

```bash
# Сборка
gcc -o oyster main.c compiler.c vm.c -lm -O2

# Компиляция и запуск
./oyster script.osf

# Только компиляция
./oyster -c script.osf

# Компиляция с исходными комментариями в байт-коде
./oyster -s script.osf

# Расширенный режим (float, edecimal, postfix, методы)
./oyster -e script.osf

```


### Опции компилятора
Флаг	Описание
* -c	Только компиляция, без выполнения
* -s	Добавлять исходные строки как комментарии в байт-код
* -e	Расширенный режим (float, edecimal, postfix, методы)
* -o <file>	Указать имя выходного файла
* -I <path>	Добавить путь в @INC для поиска модулей
* -h, --help	Показать справку



### Типы данных

#### V_NUMBER (основной числовой тип)
64.32 fixed point. Целая часть — 64 бита, дробная — 32 бита. Обеспечивает точные десятичные дроби.

```oyster
$x = 42          # целое
$y = 3.14        # с дробной частью
$z = 0.1 + 0.2   # 0.3 (точно!)
```

#### V_STRING
Строки в двойных или одинарных кавычках на основе ByteArray.

```oyster
$name = "Oyster"
$path = '/usr/local/bin'

$hw = "Hellow,
word!" # Многострочные строки

$hw2 = "Hellow,\n word!" # Escape-последовательности (\n, \t, \\, \", \', \r)

```

#### V_ARRAY
Массивы фиксированной длины на основе ByteArray. Индексация с 0.

```oyster
@arr = (10, 20, 30, 40)
$x = @arr[0]         # 10
@arr[1] = 99         # замена элемента
$len = len(@arr)     # 4

@arr2 = array(3)    # создание пустого массива
```

#### V_HASH
Хеш на связных сегментах. Ключи — строки, значения — любые типы.

```oyster
%hash = (key1 => 100, key2 => "value")
$x = %hash["key1"]       # доступ к элементу
%hash["key3"] = 300      # добавление/замена
$exists = exists(%hash["key2"])  # проверка существования ключа

%hash = ()  # сщздание пустого хеша
hadd(%hash, "0.2.0", "version") 
print(%hash)
```
Лёгкий хеш - хеш без ключей. Создаётся присваиванием массива хешу:
```oyster
%hash = ("one", "two", "three")
%hash = array(3)
```

#### V_UNDEF
Неопределённое значение. Используется для непроинициализированных переменных.

```oyster
$x = undef($y)           # проверка на undef
$z = ifundef($maybe, 0)  # замена если undef
```

### to do в последующих релизах:

#### V_FLOAT (только с флагом -e)
IEEE 754 double. Для совместимости с аппаратной арифметикой.

```oyster
$x = 3.14f
$y = $x.sqrt()
```

#### V_EDECIMAL (только с флагом -e)
Произвольная точность. Экспоненциальная форма с суффиксом d.

```oyster
$x = 1.5d3         # 1.5 × 10³
$y = 6.022d23      # число Авогадро
```

### Переменные
```oyster
$var — скалярная переменная

@arr — массив

%hash — хеш
```

Переменные создаются при первом присваивании:

```oyster
$x = 42
@data = (1, 2, 3)
%config = (debug => 1)
```

### Константы
Именованные константы подставляются на этапе компиляции:

```oyster
&PI = 3.14159
&MAX_SIZE = 100
&GREETING = "Hello"

$circumference = 2 * &PI * $radius
```

Константы из внешних модулей доступны через префикс:

```oyster
use "math" as M
$area = &M.PI * $radius * $radius
```

### Операторы
#### Арифметические
* \+    # Сложение
* \-    # Вычитание
* \*    # Умножение
* /     # Деление
* %     # Остаток от деления
* ^     # Возведение в степень
* \-    # (унарный) Отрицание
* \++   # Инкремент
* \--   # Декремент

#### Сравнения
* ==    # Равно
* !=    # Не равно
* \<    # Меньше
* \>    # Больше
* \<=   # Меньше или равно
* \>=   # Больше или равно

#### Логические
* and   # Логическое И
* or    # Логическое ИЛИ
* not   # Логическое НЕ

#### Битовые
* &     # Битовое И
* \|    # Битовое ИЛИ
* ^^    # Битовое XOR
* \~    # Битовое НЕ
* \<<   # Сдвиг влево
* \>>   #Сдвиг вправо

#### Условные операторы

if / elseif / else:

```oyster
if ($x > 10) {
    print($x)
} elseif ($x == 100) {
    print(100)
} elseif ($x == 300) {
    print(300)
} else {
    print(0)
}
```

#### Тернарный оператор
```oyster
$max = ($a > $b) ? $a : $b
```

#### Циклы
while:
```oyster
while ($i < 10) {
    print($i)
    $i = $i + 10
}
```

for (C-style):
```oyster
for ($i = 0; $i < 100; $i = $i + 1) {
    print($i)
}
```

for (in-style):
```oyster
for $item in @array {
    print($item)
}
```

#### Управление циклом
* last      # Выход из цикла
* next      # Следующая итерация
* redo      # Повтор текущей итерации

Метки для вложенных циклов:
```oyster
OUTER: while ($i < 10) {
    INNER: for $j in @arr {
        if ($j == 5) { last OUTER }
        if ($j == 3) { next INNER }
    }
}
```

#### Функции
Определение функции:

```oyster
fun add($a, $b) {
    return $a + $b
}

$result = add(10, 20)
```

Вместо разделителя аргументов в вызове функци "," может использоваться пробел " ":
```oyster
$result = add(10 20)
```

Экспорт функций из модуля:
```oyster
# В модуле:
fun helper($x) {
    return $x * 2
}

export helper
```

В основном файле:
```oyster
use "mymodule" as M
print(M.helper(5))
```

### Встроенные функции

#### Печать
```oyster
print("Hello, World!")
```

#### Математические
* abs(x)                # Абсолютное значение
* sign(x)               # Знак числа (-1, 0, 1)
* inv(x)                # Инверсия знака
* int(x)                # Целая часть числа
* frac(x)               # Дробная часть числа
* sqrt(x)               # Квадратный корень
* inc($x)               # Инкремент переменной
* dec($x)               # Декремент переменной

#### Строковые
* len(s)                    # Длина строки
* index(s, sub, pos)        # Поиск подстроки
* rindex(s, sub, pos)       # Поиск подстроки справа
* substr(s, off, len, repl) # Извлечение/замена подстроки
* chomp(s)                  # Убрать \n в конце
* chop(s)                   # Убрать последний символ
* lc(s)                     # Нижний регистр
* uc(s)                     # Верхний регистр
* lcfirst(s)                # Первый символ в нижний регистр
* ucfirst(s)                # Первый символ в верхний регистр
* split(pat, s)             # Разбить строку в массив
* join(sep, arr)            # Собрать массив в строку
* chr(n)                    # Код символа → символ
* ord(c)                    # Символ → код символа
* strcmp(s1, s2)            # Сравнение строк

#### Файловые
* fopen(name, mode)     # Открыть файл
* fclose(fh)            # Закрыть файл
* freadline(fh)         # Прочитать строку
* fread(fh, len)        # Прочитать len байт
* fprint(fh, data)      # Записать в файл

#### Для массивов и хешей
* len(@arr)	            # Длина - массив или хеш
* clone(@x)             # Клонировать - массив или хеш
* deallocate(@x)	      # Освободить память - массив или хеш

#### Для массивов
* revers(@arr)	        # Перевернуть массив
* sort(@arr)	          # Отсортировать массив

#### Для хешей
* exists(%hash["key"])          # Проверка существования ключа
* haskeys(%h)                   # Проверка наличия ключей. Отсутствие - признак лёгкого хеша
* setkey(%h, old_key, new_key)  # Изменить ключ
* getkey(%h, index)             # Получить ключ по индексу
* hadd(%h, value, key?)         # Добавить элемент в хеш
* hdel(%h)                      # Удалить последний элемент хеша

#### Для работы с undef
* undef(x)              # Проверка: 1 если x — undef
* ifundef(x, default)   # x если не undef, иначе default

### Модули

Подключение модуля:
```oyster
use "mymodule" as M
```

Вызов функции из модуля:
```oyster
print(M.myfunc(42))
```

Использование константы из модуля
```oyster
$x = &M.PI * 2
```

Модули компилируются каскадно: при компиляции основного файла все зависимости находятся и компилируются автоматически.

Режим -e (Experimental extension)
Расширенный режим добавляет:

Постфиксная запись выражений:
```oyster
$c = postfix{ $a $b + 2.0 / }
# Эквивалентно: $c = ($a + $b) / 2.0
```

Методные вызовы:
```oyster
$x = $a.abs()          # abs($a)
$y = "hello".length()  # length("hello")
$z = 16.0.sqrt()       # sqrt(16.0)
$fh = "file.txt".fopen("r")
$arr = @data.revers()  # revers(@data)
$x.inc()               # инкремент
$x.dec()               # декремент
```

Цепочки методов:
```oyster
$result = $a.abs().sqrt().int()
```

### Примеры программ
#### Факториал
```oyster
fun factorial($n) {
    if ($n <= 1) {
        return 1
    }
    return $n * factorial($n - 1)
}

print(factorial(10))
```

#### Сумма элементов массива
```oyster
@arr = (1, 2, 3, 4, 5)
$sum = 0
for $item in @arr {
    $sum = $sum + $item
}
print($sum)
```

#### Чтение файла
```oyster
$fh = fopen("data.txt" "r")
while (!feof($fh)) {
    $line = freadline($fh)
    print($line)
}
fclose($fh)
```


## Changelog v0.3.6

✅ last/next/redo во всех циклах

✅ Метки циклов (LOOP: while(...) { last LOOP })

✅ Индексация локальных переменных (fun в любом месте)

✅ Исправлены ошибки sqrt() и @arr[...] в print()

✅ Пробел как разделитель аргументов (везде)


## Changelog v0.3.5

✅ for $x in @arr — цикл по массиву
    
✅ %h["key"] в выражениях
    
✅ setkey(%h, old, new) — изменение ключа хеша
    
✅ Escape-последовательности (\n, \t, \\, \", \', \r)
    
✅ Многострочные строки
    
✅ elseif — каскадные условия
    
✅ Хеш-таблица пула строк (O(1), без дубликатов)
    
✅ Конкатенация через +


## Changelog v0.3.4

✅ elseif — каскадные условия (полноценный if/elseif/else)

✅ Хеш-таблица для пула строк (djb2 + открытая адресация, O(1))

✅ Устранены дубликаты строк в .oce

✅ ^ — возведение в степень

✅ + — конкатенация строк + сложение чисел (автоопределение типа)

✅ Режим -s (комментарии в байт-коде)

✅ Постфикс, методы, функции, рекурсия


## Changelog v0.3.3:

✅ Регистронезависимый синтаксис

✅ Постфиксные выражения postfix{ ... }

✅ Методные вызовы $x.abs()

✅ Функции с параметрами и рекурсией

✅ return внутри if
































