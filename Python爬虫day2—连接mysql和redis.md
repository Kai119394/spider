####数据采集和解析
#####爬虫开发所需工作及相关技术简要汇总：
1. 下载数据 - urllib / requests / aiohttp
2. 解析数据 - re / lxml / beautifulsoup4（bs4）/ pyquery
3. 缓存和持久化 - pymysql / redis / sqlalchemy / peewee / pymongo
4. 序列化和压缩 - pickle / json / zlib
5. 调度器 - 进程 / 线程 / 协程

##### 定制请求头和代理
```
# 设置请求头，冒充百度爬虫（也可冒充浏览器）
headers = {'user-agent': 'Baiduspider'}
# 设置爬虫代理，隐藏自己ip地址
proxies = {
'http': 'http://101.251.232.221:3128',
}
```

#####数据持久化1（连接mysql）
```
from urllib.error import URLError
from urllib.request import urlopen

import re
import pymysql
import ssl

from pymysql import Error


# 通过指定的字符集对页面进行解码(不是每个网站都将字符集设置为utf-8)
def decode_page(page_bytes, charsets=('utf-8',)):
    page_html = None
    for charset in charsets:
        try:
            page_html = page_bytes.decode(charset)
            break
        except UnicodeDecodeError:
            pass
            # logging.error('Decode:', error)
    return page_html


# 获取页面的HTML代码(通过递归实现指定次数的重试操作)
def get_page_html(seed_url, *, retry_times=3, charsets=('utf-8',)):
    page_html = None
    try:
        page_html = decode_page(urlopen(seed_url).read(), charsets)
    except URLError:
        # logging.error('URL:', error)
        if retry_times > 0:
            return get_page_html(seed_url, retry_times=retry_times - 1,
                                 charsets=charsets)
    return page_html


# 从页面中获取需要的部分(通常是链接也可以通过正则表达式进行指定)
def get_matched_parts(page_html, pattern_str, pattern_ignore_case=re.I):
	# pattern_ignore_case = re.I表示默认不区分大小写
    pattern_regex = re.compile(pattern_str, pattern_ignore_case)
    return pattern_regex.findall(page_html) if page_html else []


# 开始执行爬虫程序并对指定的数据进行持久化操作
def start_crawl(seed_url, match_pattern, *, max_depth=-1):
    # 连接数据库
    conn = pymysql.connect(host='localhost', port=3306,
                           database='crawler', user='root',
                           password='123456', charset='utf8')
    try:
        with conn.cursor() as cursor:
            url_list = [seed_url]
            # 通过下面的字典避免重复抓取并控制抓取深度
            visited_url_list = {seed_url: 0}
            while url_list:
                current_url = url_list.pop(0)
                depth = visited_url_list[current_url]
                if depth != max_depth:
                    # 获取页面
                    page_html = get_page_html(current_url, charsets=('utf-8', 'gbk', 'gb2312'))
                    # 获取a标签
                    links_list = get_matched_parts(page_html, match_pattern)
                    param_list = []
                    for link in links_list:
                        if link not in visited_url_list:
                            visited_url_list[link] = depth + 1
                            page_html = get_page_html(link, charsets=('utf-8', 'gbk', 'gb2312'))
                            # 获取h1标签
                            headings = get_matched_parts(page_html, r'<h1>(.*)<span')
                            if headings:
                                param_list.append((headings[0], link))
                    cursor.executemany('insert into tb_result  values (default, %s, %s)', param_list)
                    # cursor.execute('insert into tb_result (headings[0], link) values (%s, %s)', (title, link))

                    conn.commit()
    except Error as error:
        logging.error('SQL:', error)
    finally:
        conn.close()


def main():
    ssl._create_default_https_context = ssl._create_unverified_context
    start_crawl('http://sports.sohu.com/nba_a.shtml',
                r'<a[^>]+test=a\s[^>]*href=["\'](.*?)["\']',
                max_depth=2)


if __name__ == '__main__':
    main()
```

#####数据持久化2（连接redis）

```
import redis  # 导入redis


def main():
	# 连接redis
	client = redis.Redis(host='localhost', port=6379, password='')
	# print(client.ping()), 校验是否连接成功
	
	resp = requests.get('http://sports.sohu.com/nba_a.shtml')
	html = resp.content.decode('gbk')
	soup = BeautifulSoup(html, 'lxml')
    for elem in soup.select('a[test=a]'):
        # 通过attrs属性(字典)获取元素的属性值
		link_url = elem.attrs['href']
		resp = requests.get(link_url)
		bs_sub = BeautifulSoup(resp.text, 'lxml')
       
		title = re.sub(r'[\r\n]', '', bs_sub.select_one('h1').text)
		# 保存数据到redis
		client.set(link_url, title)
		
		# 从redis中获取数据（需decode解码为utf-8格式）
		client.get(link_url).decode('utf-8')
	

if __name__ == '__main__':

    main()
```

#####哈希摘要、序列化及压缩
1.哈希摘要
```
hasher = hashlib.md5  # 还有sha1、sha256、sha512摘要方式
hasher.update(link.encode('utf-8'))
# 对link进行摘要，link 为页面url

```
2.序列化及压缩
```
zipped_page = zlib.compress(pickle.dumps(page_html))
# page_html 为页面
# pickle.dumps(page_html) 序列化页面
# zlib.compress 压缩；zlib.decompress
```