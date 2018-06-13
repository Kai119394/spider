###异步IO定义
* 在一个线程中，CPU执行代码的速度极快，然而，一旦遇到IO操作，如读写文件、发送网络数据时，就需要等待IO操作完成，才能继续进行下一步操作。这种情况称为同步IO。在IO操作的过程中，当前线程被挂起，而其他需要CPU执行的代码就无法被当前线程执行了。

* 因为一个IO操作就阻塞了当前线程，导致其他代码无法执行，所以我们必须使用多线程或者多进程来并发执行代码，为多个用户服务。每个用户都会分配一个线程，如果遇到IO导致线程被挂起，其他用户的线程不受影响。

* 多线程和多进程的模型虽然解决了并发问题，但是系统不能无上限地增加线程。由于系统切换线程的开销也很大，所以，一旦线程数量过多，CPU的时间就花在线程切换上了，真正运行代码的时间就少了，结果导致性能严重下降。

* 由于我们要解决的问题是CPU高速执行能力和IO设备的龟速严重不匹配，多线程和多进程只是解决这一问题的一种方法。

* 另一种解决IO问题的方法是异步IO。当代码需要执行一个耗时的IO操作时，它只发出IO指令，并不等待IO结果，然后就去执行其他代码了。一段时间后，当IO返回结果时，再通知CPU进行处理。

####协程
* 协程，又称微线程，纤程。英文名Coroutine。

* 子程序，或者称为函数，在所有语言中都是层级调用，比如A调用B，B在执行过程中又调用了C，C执行完毕返回，B执行完毕返回，最后是A执行完毕。

* 所以子程序调用是通过栈实现的，一个线程就是执行一个子程序。

* 子程序调用总是一个入口，一次返回，调用顺序是明确的。而协程的调用和子程序不同。

* 协程看上去也是子程序，但执行过程中，在子程序内部可中断，然后转而执行别的子程序，在适当的时候再返回来接着执行。

* 注意，在一个子程序中中断，去执行其他子程序，不是函数调用，有点类似CPU的中断。

#### 一个简单的协程例子
```

# 产生n
def countdown_gen(n, consumer):
	consumer.send(None)  # 预激操作，等同于next(consumer)
	while n > 0:
		consumer.send(n)
		n -= 1
	try:
		consumer.send(None)  # 发送空值，结束线程
	except StopIteration:
		pass

def countdown_con():
	while True:
		n = yeild  # 消费一个n
		if n:
			print('Countdown', n)
			sleep(1)
			else:
			break

def main():
	consumer = countdown_con()
	countdown_gen(10, consumer)

if __name__ == '__main__':
    main()	
```

#### 快递员送快递（协程）
```
from random import randint
from time import sleep
from day0604myutils import coroutine

# 快递员，送出包裹
@coroutine
def create_delivery_man(name, capacity=1):
    buffer = []
    while True:
        size = 0
        pkg_name = yield
        print('%s正在派送%s号包裹' % (name, pkg_name))
        sleep(randint(1, 3))

# 快递中心，产生包裹
def create_package_center(consumer, max_packages):
    # consumer.send(None)  预激活 next(consumer)
    num = 0
    while num <= max_packages:
        consumer.send('%d' % num)
        num += 1
        if num % 10 == 0:
            sleep(randint(15, 30))

def main():
    dm = create_delivery_man('王大锤')
    create_package_center(dm, 50)

if __name__ == '__main__':
    main()
```
```
# 预激活包装器
def coroutine(fn):

    def wrapper(*args, **kwargs):
        gen = fn(*args, **kwargs)
        next(gen)
        return gen

    return wrapper
```