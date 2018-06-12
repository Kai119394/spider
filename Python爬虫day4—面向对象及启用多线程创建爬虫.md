###一个简单的面向对象多线程爬虫
```
from enum import Enum, unique
from queue import Queue
from random import random
from threading import Thread
from time import sleep
from urllib.parse import urlparse
import requests
from bs4 import BeautifulSoup


@unique
class SpiserStatus(Enum):
	IDLE = 0
	WORKING = 1

class Retry(object):
	def __init__(self, *, retry_times=3, wait_secs=5, errors=(Exception, )):
		self.retry_times = retry_times
		self.wait_secs= wait_secs
		self.errors = errors
	
	def __call__(self, fn):
		def wrapper(*args, **wkargs):
			for _ in range(self.retry_times):
				try:
					return fn(*args, *kwargs)
				except self.errors as e:
					print(e)
					sleep(random()+1 * self.wait_secs)
				return None
			return wrapper
	
	
def decode_page(page_bytes, charsets=('utf-8', ))
	page_html = None
	for charset in charsets:
		try:
			page_html = page_bytes.decode(charset')
				break
		except UnicodeDecodeError as e:
			print(e)
	return page_html

class Spider(object):  # spider类
	def__init__(self):
		self.status = SpiderStatus.IDLE

	# 抓取页面
	@Retry()
	def fetch(self, current_url, *, charsets=('utf-8', ), user_agent=None, proxies=None)
		headers  = {'user_agent': user_agent} if user_agent else {}
		resp = requests.get(current_url, headers=headers, proxies=proxies)
		if resp.status_code == 200:
			return decode_page(resp.content, charsets)
		else:
			return None
	
	# 解析页面
	def parse(self, html_page, *, domain='m.sohu.com')：
		soup = BeatufulSoup(html_page, 'lxml')
		url_links = []
		a_tags = soup.body.select('a[ref]')
		for a_tag in a_tags:
			paeser = urlparse(a_tag.attrs['href'])
			netloc = parser.netloc or domain  # 域名
            if netloc == domain:
                scheme = parser.scheme or 'http'
                path = parser.path
                query = '?' + parser.query if parser.query else ''
                full_url = f'{scheme}://{netloc}{path}{query}'
                if full_url not in visited_urls:
                    url_links.append(full_url)
        return url_links
			
visited_url = set()		
	
class SpiderThread(Thread):
	def __init__(self, spider, task_queue):
		super().__init__(daemon=true)
		self.spider = spider
		self.task_queue = task_queue
	def run(self):
		while True:
			current_url = self.task-queue.get()
			visited_url.add(current_url)
			self.spider.status = SpiserStatus.WORKING
			
			html_page = self.spider.fetch(current_url)
			if html_page not in [None, '']:
				url_links = self.spider.parse(html_page)
				for url_link in url_links:
					self.task_queue.put(url_link)
			self.spider.status = SpiderStatus.IDLE

def main():
	task_queue = Queue()
	task_queue.put('http://m.sohu.com/')
	spider_threads = [SpiderThread(Spider(), task_queue) for _ in range(10)]
	for spider_thread in spider_threads :
		spider_thread.start()
	while not task_queque.empty() or is_any_alive(spider_thread):
		pass
	print('over')

if __name__ == '__main__':
    main()
```

###面向对象爬取搜狐网页获取数据
```
import pickle
from hashlib import sha1
from enum import Enum, unique
from random import random
from threading import current_thread, Thread
from time import sleep
from urllib.parse import urlparse
import pymongo
import redis
import requests
import zlib
from bs4 import BeautifulSoup
from bson import Binary


@unique  # 表示内部状态是唯一
class SpiderStatus(Enum):  # 状态枚举
    IDLE = 0  # 空闲
    WORKING = 1


class Retry(object):

    def __init__(self, *, retry_times=3, wait_secs=5, errors=(Exception, )):
        self.retry_times = retry_times
        self.wait_secs = wait_secs
        self.errors = errors

    def __call__(self, fn):
        # 包装器
        def wrapper(*args, **kwargs):
            for _ in range(self.retry_times):
                try:
                    return fn(*args, **kwargs)
                except self.errors as e:
                    print(e)
                    sleep((random() + 1) * self.wait_secs)
            return None
        return wrapper


# 解编码页面
def decode_page(page_bytes, charsets=('utf-8', )):
    page_html = None
    for charset in charsets:
        try:
            page_html = page_bytes.decode(charset)
            # 解码成功跳出循环
            break
        except UnicodeDecodeError:
            pass
    return page_html


# 创建spider类
class Spider(object):

    def __init__(self):
        self.status = SpiderStatus.IDLE  # 状态属性为空闲

    # 抓取页面
    @Retry()
    def fetch(self, current_url, *, charsets=('utf-8', ), user_agent=None, proxies=None):

        thread_name = current_thread().name
        print(f'[{thread_name}]: {current_url}')
        # 设置请求头
        headers = {'user-agent': user_agent} if user_agent else {}
        # 获取页面
        resp = requests.get(current_url, headers=headers, proxies=proxies)
        return decode_page(resp.content, charsets) if resp.status_code == 200 else None
        # if resp.status_code == 200:
        #     return decode_page(resp.content, charsets)
        # else:
        #     return None

    # 解析页面，查找a标签和url
    def parse(self, html_page, *, domain='m.sohu.com'):
        soup = BeautifulSoup(html_page, 'lxml')
        # 查找所有的a标签
        for a_tag in soup.body.select('a[href]'):
            # 获取url并解析
            parser = urlparse(a_tag.attrs['href'])
            scheme = parser.scheme or 'http'
            netloc = parser.netloc or domain
            if scheme != 'javascript' and netloc == domain:
                path = parser.path
                query = '?' + parser.query if parser.query else {}
                full_url = f'{scheme}://{netloc}{path}{query}'

                # sismember判断full_url是否集合成员
                if not redis_client.sismember('visited_urls', full_url):
                    redis_client.rpush('m_sohu_task', full_url)


# 创建spider的线程类
class SpiderThread(Thread):

    def __init__(self, name, spider):
        super().__init__(daemon=True)  # daemon=True 设置守护线程，主线程结束，其他线程跟着结束
        self.name = name
        self.spider = spider

    def run(self):
        while True:
            # 从集合中获取需要运行的url
            current_url = redis_client.lpop('m_sohu_task')
            while not current_url:
                current_url = redis_client.lpop('m_sohu_task')
            # 给spider绑定状态
            self.spider.status = SpiderStatus.WORKING

            current_url = current_url.decode('utf-8')

            # 判断current_url是否是已经执行过
            if not redis_client.sismember('visited_urls', current_url):
                # 创建并添加
                redis_client.sadd('visited_urls', current_url)
                html_page = self.spider.fetch(current_url)
                if html_page not in [None or '']:

                    hasher = hasher_proto.copy()
                    hasher.update(current_url.encode('utf-8'))
                    doc_id = hasher.hexdigest()
                    if not sohu_data.find_one({'_id': doc_id}):
                        sohu_data.insert_one({
                            '_id': doc_id,
                            'url': current_url,
                            'page': Binary(zlib.compress(pickle.dumps(html_page)))
                        })

                    self.spider.parse(html_page)
            self.spider.status = SpiderStatus.IDLE


# 判断是否有任何一个线程在工作
def is_any_alive(spider_threads):
    return any([spider_thread.spider.status == SpiderStatus.WORKING for spider_thread in spider_threads])


# 创建redis连接对象
redis_client = redis.Redis(host='localhost', port=6379)

# 创建mongo连接对象
mongo_client = pymongo.MongoClient(host='180.76.53.34', port=27017)
db = mongo_client.sohu  # 创建sohu数据库
sohu_data = db.webpages  # 创建webpages集合
hasher_proto = sha1()


def main():
    # 创建m_sohu_task列表并加入种子url
    if not redis_client.exists('m_sohu_task'):
        redis_client.rpush('m_sohu_task', 'http://m.sohu.com/')

    # 创建10个spider线程
    spider_threads = [SpiderThread('thread-%d' % i, Spider()) for i in range(10)]
    # 启动所有线程
    for spider_thread in spider_threads:
        spider_thread.start()

    # 判断m_sohu_task列表存在或者任何一线程在工作
    while redis_client.exists('m_sohu_task') or is_any_alive(spider_threads):
        pass

    print('over!')


if __name__ == '__main__':
    main()
```