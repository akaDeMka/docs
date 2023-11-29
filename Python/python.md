python3 -m venv имя_окружения
type(variable) - определяет тип
break - прервать цикл
continue - в начало цикла
Тернарное выражение (Ternary expression)
s = [1, 2, 3, 4]
result = True if len(s) > 5 else False
*args - Позиционные аргументы переменной длины
**kwargs - Ключевые аргументы переменной длины
for position, string in enumerate(list1, 1): - значение и позиция
    ...:     print(position, string)

all() - все true
any() - хотя бы одно

sum_arg = lambda a, b: a + b - мини-функция

if __name__ == '__main__':
sys.path

Итераторы
__iter__
__next__
__init__
__str__ и __repr__ отвечают за строковое представления объекта
In [3]: numbers = [1, 2, 3]
In [4]: i = iter(numbers)
In [5]: next(i)

Генератор
genexpr = (x**2 for x in range(10000))
next(genexpr)

time
datetime

class CiscoSSH(ConnectSSH):
    pass

import logging

In [2]: "interface FastEthernet0/%s" % '1'
Out[2]: 'interface FastEthernet0/1'

В Python есть несколько стандартных типов данных:
	Numbers (числа)
	Strings (строки) '' - неизменяемый упорядоченный тип данных
	Lists (списки) [] - изменяемый упорядоченный тип данных
	Dictionaries (словари) {} - упорядочены по порядку добавления элементов, изменяемы, ключ - объект неизменяемого типа: число, строка, кортеж
	Tuples (кортежи) () - неизменяемый упорядоченный тип данных = список, который нельзя изменить
	Sets (множества) {} - только уникальные элементы, изменяемый неупорядоченный тип данных
	Boolean (логический тип данных)

round(10/3.0, 2) - округление до 2-х знаков после запятой
10%3 - остаток от деления на 3
int(a, 2) - в 2-ичное исчисление
bin(8) - двоичное число
hex(8) - шестнадцатиричное число
import  math
math.sqrt(9)
math.factorial(3)
math.pi


Strings (строки)
str() - преобразование в строку
string1[-1] - с конца строки
string1[0:9]/string1[10:] - срез строки
a[::2] - все четные строки
a[::-1] - обратный порядок строки
len(line)
','.join(vlans) - собирает список в строку
string1.upper()
string1.lower()
string1.swapcase()
string2.capitalize()
.isdigit() - в строке только цифры
.isalpha() - в строке только буквы
.isalnum() - строка из букв и цифр

string1.count('hello') - посчет совпадений фрагмента в строке
string1.find('Fast') - первый символ искомой строки
string1.replace('Fast', 'Gigabit')
"test".startswith(("r", "t"))
"rtest".endswith(("r", "a"))
string1.strip() - убирает \t\n\r\f\v
lstrip/rstrip
.split(',') - разбивает строку на список (по умолчанию разделител - пробел)
"interface FastEthernet0/{}".format('1') - вставляет символ на место {}
print("{:15} {:15} {:15}".format(vlan, mac, intf)) - Выравнивание по левой стороне
print("{:.3f}".format(10.0/3)) - сколько цифр после запятой выводить

Lists (списки)
.append() - добавить элемент
.pop() - удаляет элемент + возвращает указанный элемент
.remove() - удаляет элемент
.index('30') - найти элемент 30
.insert() - вставить элемент на опреденное место
.sort() - сортирует список
'\n'.join(cfg_lines) - приобразут в str с разделителем \n
List comprehensions
only_digits = [int(item) for item in items if item.isdigit()]

Dictionaries (словари)
.sorted()
.clear() - позволяет очистить словарь
.copy - позволяет создать полную копию словаря
.get - запрашивает ключ, и если его нет, вместо ошибки возвращает None
.setdefault - ищет ключ, и если его нет, вместо ошибки создает ключ со значением None
.keys - возвращают специальные объекты view, которые отображают ключи
.values - возвращают специальные объекты view, которые отображают значения
.items - возвращают специальные объекты view, которые отображают пары ключ-значение
.del - Удалить ключ и значение
.update - можно обновить значения

Tuples (кортежи)
.sorted()

Sets (множества)
.add() - добавляет элемент во множество
.discard() - удалять элементы, не выдавая ошибку, если элемента в множестве нет
.clear() - очищает множество
.union() / | - Объединение множеств, также можно с помощью |
intersection() / & - Пересечение множеств

Files
file = open('file_name.txt', 'r')
Режимы открытия файлов:
r - открыть файл только для чтения (значение по умолчанию)
r+ - открыть файл для чтения и записи
w - открыть файл для записи. если файл существует, то его содержимое удаляется. если файл не существует, то создается новый
w+ - открыть файл для чтения и записи. Аналогично
a - открыть файл для дополнения записи. Данные добавляются в конец файла
a+ - открыть файл для чтения и записи. Данные добавляются в конец файла
f.read()
f.readline()
f.seek - управление позицией курсора в файле
f.readlines() - возвращает список = f.read().rstrip().split('\n')
f.write
f.writelines
close(f) - f.closed - атрибут, который позволяет проверить, закрыт файл
with open('r1.txt', 'r') as f:
     for line in f:
         print(line.rstrip())
with open('r1.txt') as src, open('result.txt', 'w') as dest - открыть несколько файлов



match term:
    case pattern-1:
         action-1
    case pattern-2:
         action-2
    case pattern-3:
         action-3
    case _:
        action-default
try:
except:
else:
finally: