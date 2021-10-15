yield是python的一个关键字，刚接触python的时候对这个关键字一知半解，掌握之后才发现这关键字有大用，本文将对yield的使用方法好好梳理一番。

## 1 使用yield创建生成器

在python中，生成器是一种可迭代对象，但可迭代对象不一定是生成器。 例如，list就是一个可迭代对象

```python
>>> a = list(range(3))
>>> for i in a:
    print(i)

0
1
2
3
```

但是一个list对象所有的值都是放在内存中的，如果数据量非常大的话，内存就有可能不够用；这种情况下，可以使用生成器：

```python
>>> b = (x for x in range(3))
>>> for i in b:
    print(i)

0
1
2
>>> for i in b:
    print(i)

>>>
```

生成器可以迭代的，并且数据实时生成，不会全部保存在内存中；值得注意的是，**生成器只能读取一次，从上面的运行结果可以看到，第二次for循环输出的结果为空**。

在实际编程中，如果一个函数需要产生一段序列化的数据，最简单的方法是将所有结果都放在一个list里返回，如果数据量很大的话，应该考虑用生成器来改写直接返回列表的函数（Effective Python, Item 16）.

```python
>>> def get_generator():
    for i in range(3):
        print('gen ', i)
        yield i

>>> c = get_generator() 
>>> c = get_generator()
>>> for i in c:
    print(i)

gen  0
0
gen  1
1
gen  2
2
```

由上面的代码可以看出，当调用get_generator函数时，并不会执行函数内部的代码，而是返回了一个迭代器对象，在用for循环进行迭代的时候，函数中的代码才会被执行。 除了使用for循环获得生成器返回的值，还可以使用next和send

```python
>>> c = get_generator()
>>> print(next(c))
gen  0
0
>>> print(next(c))
gen  1
1
>>> print(next(c))
gen  2
2
>>> print(next(c))
Traceback (most recent call last):
  File "<pyshell#59>", line 1, in <module>
    print(next(c))
StopIteration
>>> c = get_generator()
>>> c.send(None)
gen  0
0
>>> c.send(None)
gen  1
1
>>> c.send(None)
gen  2
2
>>> c.send(None)
Traceback (most recent call last):
  File "<pyshell#66>", line 1, in <module>
    c.send(None)
StopIteration
```

**生成器的结果读取完后，会产生一个StopIteration的异常**

## 2 coroutines中使用

yield一个常见的使用场景是通过yield来实现协程，已下面这个生产者消费者模型为例：

```python
# import logging
# import contextlib

# def foobar():
#     logging.debug('Some debug data')
#     logging.error('Some error data')
#     logging.debug('More debug data')


# @contextlib.contextmanager
# def debug_logging(level):
#     logger = logging.getLogger()
#     old_level = logger.getEffectiveLevel()
#     logger.setLevel(level)
#     try:
#         yield
#     finally:
#         logger.setLevel(old_level)


# with debug_logging(logging.DEBUG):
#     print('inside context')
#     foobar()
# print('outside context')
# foobar()

def consumer():
    r = 'yield'
    while True:
        print('[CONSUMER] r is %s...' % r)
        #当下边语句执行时，先执行yield r，然后consumer暂停，此时赋值运算还未进行
        #等到producer调用send()时，send()的参数作为yield r表达式的值赋给等号左边
        n = yield r #yield表达式可以接收send()发出的参数
        if not n:
            return # 这里会raise一个StopIteration
        print('[CONSUMER] Consuming %s...' % n)
        r = '200 OK'

def produce(c):
    c.send(None)
    n = 0
    while n < 5:
        n = n + 1
        print('[PRODUCER] Producing %s...' % n)
        r = c.send(n)   #调用consumer生成器
        print('[PRODUCER] Consumer return: %s' % r)
    c.send(None)    
    c.close()

c = consumer()
produce(c)
[CONSUMER] r is yield...
[PRODUCER] Producing 1...
[CONSUMER] Consuming 1...
[CONSUMER] r is 200 OK...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 2...
[CONSUMER] Consuming 2...
[CONSUMER] r is 200 OK...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 3...
[CONSUMER] Consuming 3...
[CONSUMER] r is 200 OK...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 4...
[CONSUMER] Consuming 4...
[CONSUMER] r is 200 OK...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 5...
[CONSUMER] Consuming 5...
[CONSUMER] r is 200 OK...
[PRODUCER] Consumer return: 200 OK
Traceback (most recent call last):
  File ".\foobar.py", line 51, in <module>
    produce(c)
  File ".\foobar.py", line 47, in produce
    c.send(None)
StopIteration
```

在上面的例子中可以看到，yield表达式与send配合，可以起到交换数据的效果，

```text
n = yield r

r = c.send(n)
```

## 3 contextmanager中使用

另外一个比较有意思的使用场景是在contextmanager中，如下：

```python
import logging
import contextlib

def foobar():
    logging.debug('Some debug data')
    logging.error('Some error data')
    logging.debug('More debug data')


@contextlib.contextmanager
def debug_logging(level):
    logger = logging.getLogger()
    old_level = logger.getEffectiveLevel()
    logger.setLevel(level)
    try:
        yield #这里表示with块中的语句
    finally:
        logger.setLevel(old_level)


with debug_logging(logging.DEBUG):
    print('inside context')
    foobar()
print('outside context')
foobar()
inside context
DEBUG:root:Some debug data
ERROR:root:Some error data
DEBUG:root:More debug data
outside context
ERROR:root:Some error data
```

在上面的代码中，通过使用上下文管理器（contextmanager）来临时提升了日志的等级，yield表示with块中的语句；

## 总结

yield表达式可以创建生成器，应该考虑使用生成器来改写直接返回list的函数；

由于生成器只能读取一次，因此使用for循环遍历的时候要格外注意；生成器读取完后继续读的话会raise一个StopIteration的异常，实际编程中可以使用这个异常来作为读取终止的判断依据；

yield一个常见的使用场景是实现协程；通过与send函数的配合，可以起到交换数据的效果；

yield还可以在contextmanager修饰的函数中表示with块中的语句；