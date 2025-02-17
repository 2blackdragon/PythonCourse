# Занятие 2. "Продвинутый" python

## Функциональный стиль (обработка коллекций, лямбды)
Функциональный подход заключается в том, что программа рассматривается как набор функций, которые работают с неизменяемыми значениями, а также не изменяют состояние системы в целом. Такие функции в рамках терминалогии ФП называются - чистыми.

Питон предоставляет 5 инструментов ФП:
1. lambda - функции
2. filter()
3. map()
4. zip()
5. reduce()

### lambda

Лямбда-функции - это анонимные функции, т.е. функции без имени, чаще всего их можно встретить при вызове других функций и методов языка, которые требуют на вход аргумент типа Callable(функцию).
Их ключевая особенность - не нужно писать оператор return.
Пример:

```python 
multiple = lambda x, y: x*y
multiple(3,4) #12
```

Таким образом мы объявили анонимную функцию, которая будет возвращать произведение аргументов.
Лямбда функции принято записывать в одну строку, т.к. они представляют элементарное преобразование(1 простую операцию, например, арифметическое выражение, логическое выражение или выбор элемента из последовательности)

### filter()

Данная функция нужна для фильтрации последовательности.

На вход принимает функцию-фильтр, т.е. функцию, которая возвращает True/False в зависимости от элемента (обычно используют lambda функции).

Например:

```python 
mixed = ['мак', 'просо', 'мак', 'мак', 'просо', 'мак', 'просо', 'просо', 'просо', 'мак']
zolushka = list(filter(lambda x: x == 'мак', mixed)) # ['мак', 'мак', 'мак', 'мак', 'мак']
```
**Важно:** функция возвращает не последовательность, а свой объект-обертку, поэтому чтобы использовать его дальше, нужно привести его к нужному типу вручную, данное поведение актуально для всех остальных функций.

### map()

Это функция, которая служит для преобразования последовательности в новую, т.е. функция которая подаётся на вход `map()` должна возвращать какое-то значение.

Например:
```python 
mile_distances = [1.0, 6.5, 17.4, 2.4, 9]
kilometer_distances = list(map(lambda x: x * 1.6, mile_distances)) # [1.6, 10.4, 27.84, 3.84, 14.4]
```

Также в отличие от `filter()` данная функция может применяться на нескольких списках сразу, единственный нюанс, в функции-переобразователе, надо указывать столько аргументов, сколько списков передаете.

Например:

```python 
l1 = [1,2,3]
l2 = [4,5]
 
new_list = list(map(lambda x,y: x + y, l1, l2)) # [5,7]
```
Также при несовпадении количества элементов в последовательностях, количество элементов в результирующей будет равно количеству самой маленькой, как в примере выше.

### zip()

Применяется для объединения в кортеж передаваемых последовательностей.

Например:

```python 
a = [1,2,3]
b = "xyz"
c = (None, True)
 
res = list(zip(a, b, c)) # [(1, 'x', None), (2, 'y', True)]
```
Обратите внимание, что принцип работы в случае несовпадения размеров последовательностей аналогичен функции `map()`.

### reduce()

Применяется, для того чтобы получить из последовательности 1 значение, например, найти максимум или сложить все элементы.

Например:

```python
from functools import reduce
items = [1,2,3,4,5]
sum_all = reduce(lambda x,y: x + y, items) # 15

all_max = reduce(lambda a,b: a if (a > b) else b, items) # 5
```

Кстати, в примере выше есть конструкция `expression if (condition) else expression` - это так называемый тернарный оператор, позволяющий сократить условие if-else до 1 строки.

### Полезные материалы:
1. https://habr.com/ru/post/555378/ - о ФП в питоне
2. https://habr.com/ru/company/otus/blog/573164/ - functools
3. http://pythonicway.com/python-functinal-programming - о функциях ФП в питоне

## Генераторы

Генераторы нужны для работы с большими объемами данных, по сути генератор это своего рода реализация паттерна итератор.
Генератор создается с помощью ключевого слова `yield` или с помощью `()` по аналогии с созданием последовательностей.

Например:

```python
# Создание генератора через yield
def infinite_sequence():
    num = 0
    while True:
        yield num
        num += 1

gen = infinite_sequence()

next(gen) # 1
next(gen) # 2
next(gen) # 3
next(gen) # 4

# Создание генератора через ()

gen = (x for x in range(10000000))

next(gen) # 1
next(gen) # 2
next(gen) # 3
next(gen) # 4
```
Ключевая особенность генератора состоит в том, что он не помещает всю последовательность с которой работает в память, 
а отдает нам по одному кусочку последовательности в момент вызова функции `next()` или обхода его через цикл for.

Такое поведение достигается тем, что ключевое слово `yield` не завершает функцию, как `return`, а просто приостанавливает ее выполнение, до следующего вызова метода `next()`.
Благодаря этому, мы можем каждый раз получать новый элемент последовательности.

### Полезные материалы

1. https://pythonist.ru/generatory-python-ih-sozdanie-i-ispolzovanie/ генераторы

## Замыкания и декораторы

Замыкание - это функция, которая объявляется внутри другой функции и возвращается в качестве ее результата.

Такая механика, позволяет использовать нам контекст(переменные) внешней функции внутри возвращаемой.
По сути, механизм замыканий нужен для создания декораторов, или если нам нужно сделать условную фабрику функций.

Например:
```python
def multiple_fabric(n):
    def multiple(x):
        return n * x
    return multiple

double_value = multiple_fabric(2) # -> multiple(x): 2 * x
triple_value = multiple_fabric(3) # -> multiple(x): 3 * x

double_value(21) # 42
triple_value(13) # 39

```

Теперь перейдем к декораторам.

Декоратор — паттерн проектирования, при использовании которого класс или функция изменяет, 
или дополняет функциональность другого класса или функции без использования наследования или прямого изменения исходного кода.
Простыми словами, декоратор - это обертка над функцией или классом, которая позволяет расширить функциональность последних.

В Python декораторы представляют собой функции или любые вызываемые объекты, 
которые принимают на вход набор необязательных аргументов и функцию или класс и возвращают функцию или класс. 
Декораторы объявляются с помощью символа `@`.

Например:
```python
@decorator
def hello():
    pass
```

Рассмотрим работу декораторов поближе:
```python
def decorator_function(func):
    def wrapper():
        print('Функция-обёртка!')
        print('Оборачиваемая функция: {}'.format(func))
        print('Выполняем обёрнутую функцию...')
        func()
        print('Выходим из обёртки')
    return wrapper

@decorator_function
def hello_world()
    print("Hello, World!")

hello_world()
# Функция-обёртка! <- декоратор
# Оборачиваемая функция: hello_world <- декоратор
# Выполняем обёрнутую функцию... <- декоратор
# Hello, World! <- наша функция
# Выходим из обёртки <- декоратор
```

Как объяснялось выше, декораторы - это обертки над функциями и методами классов, 
благодаря чему, мы можем отделить часть повторяющейся логики, от целевой функции или метода и значительно упростить его.

Например, нам нужно реализовать механизм повтора выполнения какой-либо функции в случае ошибки, 
это может быть полезно, если нам надо гарантированно выполнить какое-то действие, например, записать что-то в базу данных.

```python
def retry(max_retries): # "Фабрика" декораторов (по аналогии с замыканием)
    def retry_decorator(func): # Реальный декоратор
        def _wrapper(*args, **kwargs): # Обертка над функцией, *args и 
            for _ in range(max_retries):
                try:
                    func(*args, **kwargs)
                except:
                    time.sleep(1)
        return _wrapper
    return retry_decorator

@retry(2)
def save_data(value):
    print(value)
    raise Exception # специально упадем, чтобы проверить

save_data('SaveME')
# SaveME
# ждем 1 секунду
# SaveME
# ждем 1 секунду
# ОШИБКА
```

Также декораторы помогают в случаях, когда надо передать что-то в функцию, т.е. выполнить так называемую инъекцию зависимостей.

### Полезные ссылки
1. https://habr.com/ru/company/wunderfund/blog/657355/ декораторы
2. https://tproger.ru/translations/demystifying-decorators-in-python/ еще декораторы
3. https://medium.com/nuances-of-programming/5-важных-аспектов-замыканий-в-python-68a11c7ef627 замыкания
4. https://ru.hexlet.io/blog/posts/izuchaem-prodvinutye-vozmozhnosti-python-chast-2-zamykaniya-dekoratory-modul-functools замыкания и декораторы

## Обработка ошибок

Ошибки не обошли питон стороной. Для обработки ошибок используется следующий синтаксис:
```python
try:
    do_something()
except Exception: # обязательный блок, выполняется когда блок try выбросил ошибку, может быть несколько, зависит от количества возможных ошибок
    do_something_on_exception()
else: # необязательный блок, выполняется в случаях когда блок try не выбросил ошибки
    do_something_on_success()
finaly: # необязательный блок, выполняется ВСЕГДА независимо от того, была ошибка или не было, также выполняется даже если до него сработал return
    do_something_finnaly()
```

Пример:
```python
try:
    print(1 / int(input()))
except ZeroDivisionError:
    print("Ошибка деления на ноль.")
except ValueError:
    print("Невозможно преобразовать строку в число.")
except Exception:
    print("Неизвестная ошибка.")
else:
    print("Операция выполнена успешно.")
finally:
    print("Программа завершена.")
```

Список ошибок:


### Полезные материалы
1. https://academy.yandex.ru/handbook/python/article/model-isklyuchenij-python-try-except-else-finally-moduli - по ошибкам
2. https://pythonworld.ru/tipy-dannyx-v-python/isklyucheniya-v-python-konstrukciya-try-except-dlya-obrabotki-isklyuchenij.html и это тоже

## Особенности ООП

ООП - это еще один подход к проектированию и разработке ПО, который строится на том, что мы можем отразить окружающий нас мир в виде абстрактных моделей - классов.

ООП строится на 4-х концепциях:
* Абстракция
* Наследование
* Инкапсуляция
* Полиморфизм

### Абстракция

Это принцип максимального ухода от реализации, т.е. ваша сущность, которую вы создаете должна быть предельно простой 
и максимально точно описывать НЕОБХОДИМЫЕ поведение и свойства описываемого объекта реального мира.

Условно у нас есть машина, которая обладает рядом свойств:
1. Двигаться с какой-то скоростью
2. У нее есть цвет
3. У нее есть модель

Но нас совершенно не волнует расход ее двигателя или время набора 100км/ч.

Пример:

```python
class Car: # объявление класса
    color: str
    model: str
    
    def __init__(self, color: str, model: str): # конструктор класса
        self.color = color
        self.model = model
       
    def move(self, speed): # метод класса
        pass 
```

Во всех методах класса, кроме тех, что помечены `@classmethod` или `@staticmethod`, первым аргументом обязательно идет аргумент-контекста, 
который передает в метод текущий объект класса, обычно его называют `self` - это аналог `this` в других ЯП.

### Наследование

Можно объявить базовый класс, обладающий минимальным набором аттрибутов и методов и далее объявлять дочерни классы(наследники), 
которые будут расширять его функциональность.

Например.
```python
class Animal:
    def move(self, x, y):
        pass
    
    def sound(self):
        pass

class Sheep(Animal):

    def __init__(self):
        super().__init__(self) # вызов конструктора базового класса, это важно
    
    def sound(self):
        print('бе')

class Cow(Animal):

    def __init__(self):
        super().__init__(self)
       
    def give_milk(self):
        pass
        
    def sound(self):
        print('му')
```

Также питон поддерживает множественное наследование, т.е. мы можем указывать больше 1 родительского класса при объявлении дочернего,
но с этим нужно быть предельно осторожным. Т.к. если вы попробуете наследоваться от 2-х классов имеющих один метод и при этом не переопределите его,
то при вызове этого метода из объекта дочернего класса, вызовется метод первого класса-родителя.

Пример:

```python
class A:
    def say(self):
        print('Hi, A')
        
class B(A):
    def say(self):
        print('Hi, B')
        
class C(A):
    def say(self):
        print('Hi, C')
        
class D(B, C):
    pass
    
class F(C, B):
    pass

d = D()
f = F()

d.say()  # Hi, B
f.say()  # Hi, C
```


### Полиморфизм

Основан на том, что конечная реализация метода выбирается в момент выполнения программы, т.е. если мы вызовем метод `say()` класса `B` 
из предыдущего примера, то у нас выведется именно `Hi, B`, а не `Hi, A` как прописано в родительском методе.

### Инкапсуляция

Инкапсуляция - это ограничение видимости аттрибутов и методов класса, в отношении питона этот замечательный принцип действует на уровне соглашения, 
т.е. программисты просто договорились о том, что если перед методом стоит символ `_`, то он считается приватным и вызывать его извне нельзя.

На самом деле, в питоне есть своеобразный способ создания "защищенного" от доступа снаружи аттрибута или метода - указать `__` перед его именем.

Например:
```python
class Figure:

    def __init__(self, color):
        self.__color = color

    @property #эта аннотация позволит нам обращаться к этому методу не как к методу, а как к аттрибуту, т.е. figure.color
    def color(self):
        return self.__color

    @color.setter # а вот эта аннотация объявляет сеттер, т.е. метод простановки значения с именем color
    def color(self, c):
        self.__color = c

```

Благодаря использованию аннотаций `@property` и `@<attribute-name>.setter` можно реализовать работу со скрытыми аттрибутами, 
обе эти аннотации позволяют обращаться к методам как к аттрибутам класса, т.е. без `()`, а с помощью перегрузки методов(это когда можно сделать методы с одним названием, но разным количеством аргументов или их типов)
можно сделать геттер и сеттер с одним именем.

Тем не менее этот метод все равно не дает 100% защиты от доступа извне, потому что скрытые через `__` методы и аттрибуты по-прежнему доступны, 
если вызвать их вот так: `<object-name>._ClassName__attribute`.

Например:

```python
figure = Figure("RED")

print(figure.__color) # Exception!
print(figure.color) # RED
print(figure._Figure__color) # RED
```

### `@classmethod` и `@staticmethod`

Данные декораторы позволяют нам создавать методы класса и статичные методы, в отличие от обычных методов, они могут быть вызваны без инициализации объекта.

`@staticmethod` нужен в случаях, когда мы хотим сделать что-то без необходимости доступа к контексту класса, например, класс-утилиту с мелкими функциями.

Например:

```python
class SomeUtils:
    
    @staticmethod
    def count(lst: List[Any]) -> int:
        return len(lst)
```

`@classmethod` наоборот имеет доступ к содержимому класса (не объекта), из-за этого в качестве первого аргумента, обязательно надо передавать `cls`.

Например:
```python
class MyClass():
    
    TOTAL_OBJECTS=0
    
    def __init__(self):
        MyClass.TOTAL_OBJECTS = MyClass.TOTAL_OBJECTS + 1
       
    @classmethod
    def total_objects(cls):
        print("Total objects: ", cls.TOTAL_OBJECTS)
# Создаем объекты        
my_obj1 = MyClass()
my_obj2 = MyClass()
my_obj3 = MyClass()
# Вызываем classmethod 
MyClass.total_objects() # 3
```

`@classmethod` используется в суперклассе для определения того, как метод должен вести себя, когда он вызывается разными дочерними классами. 
В то время как `@staticmethod` используется, когда мы хотим вернуть одно и то же, независимо от вызываемого дочернего класса.

### Полезные материалы
1. https://proglib.io/p/python-oop расширенное обьяснение ООП в питоне
2. https://academy.yandex.ru/handbook/python/article/obuektnaya-model-python-klassy-polya-i-metody немного о том же, но от яндекса
3. https://academy.yandex.ru/handbook/python/article/volshebnye-metody-pereopredelenie-metodov-nasledovanie продолжение ооп от яндекса
4. https://webdevblog.ru/obyasnenie-classmethod-i-staticmethod-v-python/ про `@classmethod` и `@staticmethod`
5. https://docs-python.ru/tutorial/vstroennye-funktsii-interpretatora-python/funktsija-super/ немного о `super()`


# Домашняя работа

Перепишите код из предыдущего задания, вынесите Счет в отдельный класс, с тремя полями, пропишите для них геттеры и сеттеры через аннотации `@property` и `@<>.setter`.
Переделайте функции работы со счетами в методы, попробуйте применить ФП (подсказка, функция `filter`), 
а также особо пытливые могут попробовать реализовать валидацию аргументов через декораторы, например, в методе перевода средств, 
можно вынести проверку на отрицательность суммы перевода в декоратор и выбрасывать ошибку если условие нарушается.