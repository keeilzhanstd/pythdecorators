# @Decorators
 
Декоратор — это функция, которая позволяет обернуть другую функцию для расширения её функциональности без непосредственного изменения её кода.  

<img src="https://github.com/keeilzhanstd/pythdecorators/blob/main/370569D9-94A7-4DD4-A81A-025F6B9D6304.jpeg" width="350px">

***
Представим, вы владелец огромного поля с яблонями. Прикинем, что по статистике, каждое третье яблоко сгнившее. Яблонь очень много, потому у вас появился новенький робот собирающий яблоки, и отсеивающий гнилые.  

<img src="https://github.com/keeilzhanstd/pythdecorators/blob/main/7BDC0148-ECE3-4455-AA27-D42BCECE8054.jpeg" width="750px">


###### Code

```python
TREES_NUMBER = 100000

class Robot:
	def __init__(self, name):
		self.name = name

	def collectApples(self):
		loot = [x for x in range(TREES_NUMBER) if x % 3 != 0]
		return loot
```

При первом урожае, вы пробуете запустить робота, назовём его Лутти.  
<img src="https://github.com/keeilzhanstd/pythdecorators/blob/main/F44A153C-2715-4E35-8C44-45D8E8AD5C85.jpeg" width="350px">

Лутти отлично справился со своей задачей.

```python
lootie = Robot('Lootie')
collectedApples = lootie.collectApples()
print(collectedApples)
```

###### Output
> [1, 2, 4, 5, 7, 8, 10, 11, 13, ..., 99997, 99998]



В один из дней, Вы вдруг захотели узнать насколько эффективен ваш робот. Можем добавить таймер в функцию Робота ```collectApples()```.
> Для использования datetime.now() требуется импортировать модуль datetime ```from datetime import datetime```
```python
def collectApples(self):
		start = datetime.now()
		loot = [x for x in range(TREES_NUMBER) if x % 3 != 0]
		print(datetime.now() - start)
		return loot
```

###### Output
> Robot Lootie spent 0:00:00.013960 time collecting stuff.  
> [1, 2, 4, 5, 7, 8, 10, 11, 13, ..., 99997, 99998]

  
***
<img src="https://github.com/keeilzhanstd/pythdecorators/blob/main/D9587478-1B84-47EC-9697-9C6EC5CC2E85.jpeg" width="750px">
Робот Лутти достаточно эффективен, и ваш бизнес идёт в гору.   Вы расширяетесь, и выкупаете поля с *виноградом* и *апельсинами*.

Планируя использовать Лутти для сбора урожая с виноградных  и апельсиновых полей, вы вносите изменения в его код.

>Для статистики, неудачными являются каждый 12-ый плод винограда, и каждый 6-ой апельсин.

```python
class Robot:
	def __init__(self, name):
		self.name = name

	def collectApples(self):
		start = datetime.now()
		loot = [x for x in range(TREES_NUMBER) if x % 3 != 0]
		print(f'Robot {self.name} spent {datetime.now() - start} time collecting stuff.')
		return loot

	def collectGrapes(self):
		start = datetime.now()
		loot = [x for x in range(TREES_NUMBER) if x % 12 != 0]
		print(f'Robot {self.name} spent {datetime.now() - start} time collecting stuff.')
		return loot

	def collectOranges(self):
		start = datetime.now()
		loot = [x for x in range(TREES_NUMBER) if x % 6 != 0]
		print(f'Robot {self.name} spent {datetime.now() - start} time collecting stuff.')
		return loot
        
```

А теперь, обратите внимание! Для каждой функциональной возможности Лутти мы повторяемся, используя один и тот же код для оценки эффективности Робота.  
Тут в дело вступают декораторы.

Декоратор позволит нам обернуть функции Лутти, дополнительным функционалом. В нашем случае, позволит высчитывать эффективность каждой из функции Лутти. Воплотим данный декоратор в жизнь следущим образом:

```python
def getEfficiency(method):
	def wrapper(*args, **kwargs):
		start = datetime.now()
		loot = method(*args, **kwargs)
		print(f'Robot spent {datetime.now() - start} time collecting stuff.')
		return loot
	return wrapper
```

Попробуем запустить робота собирать урожай с полей.  
Итоговый код выглядит так:

```python
from datetime import datetime

# Decorators
TREES_NUMBER = 100000

def getEfficiency(method):
	def wrapper(*args, **kwargs):
		start = datetime.now()
		loot = method(*args, **kwargs)
		print(f'Robot spent {datetime.now() - start} time collecting stuff.')
		return loot
	return wrapper

class Robot:
	def __init__(self, name):
		self.name = name

	@getEfficiency
	def collectApples(self):
		loot = [x for x in range(TREES_NUMBER) if x % 3 != 0]
		return loot

	@getEfficiency
	def collectGrapes(self):
		loot = [x for x in range(TREES_NUMBER) if x % 12 != 0]
		return loot

	@getEfficiency
	def collectOranges(self):
		loot = [x for x in range(TREES_NUMBER) if x % 6 != 0]
		return loot


lootie = Robot('Lootie')
collectedApples = lootie.collectApples()
collectedGrapes = lootie.collectGrapes()
collectedOranges = lootie.collectOranges()
```

###### Output
>Robot spent 0:00:00.012938 time collecting stuff.  
Robot spent 0:00:00.012966 time collecting stuff.  
Robot spent 0:00:00.013557 time collecting stuff.  

Видно, что Лутти справился с задачей, однако мы не на какой именно из плодов Лутти потратил то или иное время. Решим проблемы, передавая параметры в функции.

```python
from datetime import datetime

# Decorators
TREES_NUMBER = 100000

def getEfficiency(method):
	def wrapper(*args, **kwargs):
		start = datetime.now()
		loot = method(*args, **kwargs)
		print(f'Robot spent {datetime.now() - start} time collecting {args[1]}.')
		return loot
	return wrapper

class Robot:
	def __init__(self, name):
		self.name = name

	@getEfficiency
	def collectApples(self, fruit):
		loot = [x for x in range(TREES_NUMBER) if x % 3 != 0]
		return loot

	@getEfficiency
	def collectGrapes(self, fruit):
		loot = [x for x in range(TREES_NUMBER) if x % 12 != 0]
		return loot

	@getEfficiency
	def collectOranges(self, fruit):
		loot = [x for x in range(TREES_NUMBER) if x % 6 != 0]
		return loot


lootie = Robot('Lootie')
collectedApples = lootie.collectApples("Apples")
collectedGrapes = lootie.collectGrapes("Oranges")
collectedOranges = lootie.collectOranges("Grapes")
```

###### Output
>Robot spent 0:00:00.013922 time collecting Apples.  
Robot spent 0:00:00.014959 time collecting Oranges.  
Robot spent 0:00:00.013964 time collecting Grapes.  


Для передачи параметров в обернутую функцию мы можем использовать ```*args, **kwargs```  
Возможно вы заметили, что мы использовали эти аргументы и до передачи названия фрукта.  
Да, мы использовали их, поскольку мы отправляли в функцию аттрибут ```self```, он находился под индексом 0 в ```*args```.
Теперь мы имеем параметр fruit в ```*args``` под индексом 1 и можем обращаться к нему в нашем декораторе.

***

Рассмотрим еще один пример возможного использования декораторов. Ранее мы оборачивали функцию для того, чтобы засечь время, за которое Лутти собирает урожай. Теперь мы хотим научить Лутти считать! Тоесть, выполнять базовые арифметические операции. Выглядит это так, вы говорите ему операцию и два числа, а он вам ответ!


```python
class Robot:
	def __init__(self, name):
		self.name = name

	def add(self, num1, num2):
		return num1 + num2

	def subtract(self, num1, num2):
		return num1 - num2

	def divide(self, num1, num2):
		return num1 / num2

	def multiply(self, num1, num2):
		return num1 * num2

lootie = Robot('Lootie')
addition = lootie.add(1, 5)
subtraction = lootie.subtract(5, 3)
division = lootie.divide(4, 2)
multiplication = lootie.multiply(2, 5)
print(addition, subtraction, division, multiplication)
```


###### Output
> 6 2 2.0 10

<img src="https://github.com/keeilzhanstd/pythdecorators/blob/main/50D56569-882D-4F53-B360-1178F63C8208.jpeg" width="650px">



Все хорошо, до тех пор пока вы говорите числа. Но что если сказать Лутти умножить две строки?  Конечно вы получите ошибку...  
```multiplication = lootie.multiply("Lootie", "the Robot")```  
> TypeError: can't multiply sequence by non-int of type 'str'  

<img src="https://github.com/keeilzhanstd/pythdecorators/blob/main/8CAEFB97-4703-471E-A5C5-4F1514A1F0ED.jpeg" width="650px">

Поэтому надо научить Лутти распознавать числа!  
Пишем декоратор, который проверит являются ли два аргумента числами. Если нет, Лутти скажет нам об этом, и попросит сказать ему числа.


```python
def checkIfArgumentsAreIntegers(function):
	def wrapper(*args, **kwargs):
    	# args = [self, num1, num2]
		if not isinstance(args[1], int) or not isinstance(args[2], int):
			print("This is not correct question! Give Lootie 2 integers!")
			#raise TypeError('Arguments must be integers.')
		else:
			return function(*args, **kwargs)
	return wrapper	
```
Так, в следущий раз, перед тем как Лутти примется за решение задачи, он проверит а являются ли аргументы числами? Для этого обернем функции Лутти нашим декоратором.
```python
class Robot:
	def __init__(self, name):
		self.name = name

	@checkIfArgumentsAreIntegers
	def add(self, num1, num2):
		return num1 + num2

	@checkIfArgumentsAreIntegers
	def subtract(self, num1, num2):
		return num1 - num2

	@checkIfArgumentsAreIntegers
	def divide(self, num1, num2):
		return num1 / num2

	@checkIfArgumentsAreIntegers
	def multiply(self, num1, num2):
		return num1 * num2

lootie = Robot('Lootie')
print(lootie.add(1, 5))
print(lootie.subtract(5, 3))
print(lootie.divide(4, 2))
print(lootie.multiply("Lootie", "the Robot"))
```

###### Output
>6  
2  
2.0  
This is not correct question! Give Lootie 2 integers!  
None

<img src="https://github.com/keeilzhanstd/pythdecorators/blob/main/6C1B9A66-83CC-48D2-BF1E-FB9D953C6683.jpeg" width="650px">

Так используя декораторы, программисты могут писать тесты, чтобы в будущем при изменении функции, прогнать код по тестам, и удостовериться что все работает правильно. Например, мы можем написать тест на операцию сложения он будет выглядить так:

```python
def assertSummation(function):
	def wrapper(*args, **kwargs):
		res = function(*args, **kwargs)
        # args = [self, num1, num2]
		if res != args[1] + args[2]:
			print("Asserting summation function failed. Please check implementation.")
			#raise ValueError("Asserting summation function failed. Please check implementation.")
		else:
			return res
	return wrapper
```

Таким образом, если в будущем мы случайно поменяем фунцию ```add()``` 
> В данном примере функция add выполняет вычетание вместо сложения.

```python
class Robot:
	def __init__(self, name):
		self.name = name

	@checkIfArgumentsAreIntegers
	@assertSummation
	def add(self, num1, num2):
		return num1 - num2
        
lootie = Robot('Lootie')
print(lootie.add(1, 5))
print(lootie.add("Five", 3))
```

###### Output
>Asserting summation function failed. Please check implementation.  
None  
This is not correct question! Give Lootie 2 integers!  
None  
 

Обратите внимание, что можно обернуть функцию и несколько раз. В этом примере, обе наших проверки провалились :( 
Вы можете придумать и множество других тестов для своих функций в будущем! Как пример, проверить не является ли второе число Нулём при делении, и так далее.**

Это не самое полное описание декораторов, с помощью них можно творить магию. Декораторы часто используются во внешних модулях, например *Django*, либо *Flask* и совсем не зря!

***

Перед тем как закончить хочу вам показать по-моему скромному мнению - самый полезный *встроенный* декоратор из стандартной библиотеки Python functools.
Этот декоратор @lru_cache

```python

from datetime import datetime
from functools import lru_cache

def getTimeSpent(method):
	def wrapper(*args, **kwargs):
		start = datetime.now()
		fibbo = method(*args, **kwargs)
		print(datetime.now() - start)
		return fibbo
	return wrapper

@getTimeSpent
def calculateFibbo(n):
	return f'fibonacci num for {n}: {fibonacci(n)}'

def fibonacci(n):
    if n == 1 :
        return 1
    elif n == 2:
        return 2
    elif n > 2:
        return fibonacci(n-1) + fibonacci(n-2)

print(calculateFibbo(40))
```
Данный код потребовал достаточно много времени!
###### Output
>0:00:42.268020  
fibonacci num for 40: 165580141  
[Finished in 42.4s]

Использование lru_cache позволяет нам сохранять предыдущие значения функций. Посмотрите как изменилось время выполнения!
```python
@lru_cache(maxsize=3)
def fibonacci(n):
    if n == 1 :
        return 1
    elif n == 2:
        return 2
    elif n > 2:
        return fibonacci(n-1) + fibonacci(n-2)
```
###### Output
>0:00:00.000997  
fibonacci num for 40: 165580141  
[Finished in 99ms]


Для примера  столько потребовалось чтобы найти 99-ое число фиббоначи  
>0:00:00.000997  
fibonacci num for 99: 354224848179261915075  
[Finished in 101ms]  
