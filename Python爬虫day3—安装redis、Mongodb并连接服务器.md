###安装redis

####下载redis源代码安装
```
# 下载源代码
wget http://download.redis.io/releases/redis-3.2.11.tar.gz  
# 解压缩
gunzip redis-3.2.11.tar.gz
# 解归档
tar -xvf redis-3.2.11.tar
cd redis-3.2.11
make && make install
```
```
# ubuntu安装redis
sudo apt-get update
sudo apt-get install redis-server
```

####配置文件
将redis-3.2.11目录下的redis.conf配置文件复制到用户主目录下并修改配置文件（如果你对配置文件不是很有把握就不要直接修改而是先复制一份再修改这个副本）
```
cd ..
cp redis-3.2.11/redis.conf redis.conf
vim redis.conf
```

绑定指定的IP和端口

![这里写图片描述](https://img-blog.csdn.net/2018053020160397?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc4MjA1MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![这里写图片描述](https://img-blog.csdn.net/20180530202035260?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc4MjA1MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

配置Redis的持久化机制 - RDB

![这里写图片描述](https://img-blog.csdn.net/20180530202411490?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc4MjA1MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

配置Redis的持久化机制 - AOF

![这里写图片描述](https://img-blog.csdn.net/20180530202447413?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc4MjA1MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

配置访问Redis服务器的验证口令

![这里写图片描述](https://img-blog.csdn.net/20180530202734818?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc4MjA1MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这样我们就完成了Redis的基本配置。

####启动redis
```
redis-server redis.conf $ # 后台运行
```

####连接服务器
```
redis-cli -h <内网(私网)IP> -p <端口号>

auth <密码>
```

####redis的相关操作
主从复制

哨兵设置

###在python程序中使用redis
```
pip install redis  # 安装redis

>>> import redis
>>> client = redis.Redis(host='1.2.3.4', port=6379, password='1qaz2wsx')
>>> client.set('username', 'admin')
True
>>> client.hset('student', 'name', 'hao')
1
>>> client.hset('student', 'age', 38)
1
>>> client.keys('*')
[b'username', b'student']
>>> client.get('username')
b'admin'
>>> client.hgetall('student')
{b'name': b'hao', b'age': b'38'}
```

###安装mongodb
####安装流程

1.wget http://www.mongodb.com/download-center#atlas  下载源代码
2.gunzip <fileame>  解压缩
3.tar -xvf <filename>  解归档
4.mv <filename1> <filename2>  修改文件名
5.mv <filename> /usr/local/ <filename>  将文件移动到usr/local目录下
6.vim .bashrc  进入用户主目录打开bashrc文件并配置
![这里写图片描述](https://img-blog.csdn.net/20180530210532668?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc4MjA1MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

7.source .bashrc  刷新应用
8.mkdir -p /data/db  创建文件夹
9.echo &PATH
10.mongod   测试是否安装成功(27017端口)
11.mongod --bind_ip 192.168.0.4 --quiet &  （私网IP）
12.mongo --host 180.76.53.34 （公网IP）
13.jobs  查看后台进程
14. fg %1  从后台拿出进程（ctrl+c后关闭进程）
15. db  判断是否连接成功（返回test）
16. use <表名>  创建数据表

###在python程序中使用mongodb
```
import pymongo

def main():
	mongodb_client = pymongo.MongoClient(host='公网IP', port=端口号)  # 创建mongodb连接对象
	db = client.库名  # 创建数据库
	example = db.集合名  # 创建集合
	
	# 插入数据
	example.insert_many([
		{'id': 1, 'url': 'www.baidu.com', 'content': 'shit'},
		{'id': 2, 'url': 'www.sina.com', 'content': 'fuck'},
		{'id': 3, 'url': 'www.qq.com', 'content': 'bitch'}
	])

	print(example.find().count())  # 统计集合数据量
	for doc in example.find().sort('id'):
		print(doc)

if __name__ == '__mian__':
	main()
	
```