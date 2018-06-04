### 网络爬虫
####简介：
网络爬虫（web crawler），以前经常称之为网络蜘蛛（spider），是按照一定的规则自动浏览万维网并获取信息的机器人程序（或脚本），曾经被广泛的应用于互联网搜索引擎。使用过互联网和浏览器的人都知道，网页中除了供用户阅读的文字信息之外，还包含一些超链接。网络爬虫系统正是通过网页中的超链接信息不断获得网络上的其它页面。正因如此，网络数据采集的过程就像一个爬虫或者蜘蛛在网络上漫游，所以才被形象的称为网络爬虫或者网络蜘蛛。
####应用领域：
在理想的状态下，所有ICP（Internet Content Provider）都应该为自己的网站提供API接口来共享它们允许其他程序获取的数据，在这种情况下爬虫就不是必需品，国内比较有名的电商平台（如淘宝、京东等）、社交平台（如腾讯微博等）等网站都提供了自己的Open API，但是这类Open API通常会对可以抓取的数据以及抓取数据的频率进行限制。对于大多数的公司而言，及时的获取行业相关数据是企业生存的重要环节之一，然而大部分企业在行业数据方面的匮乏是其与生俱来的短板，合理的利用爬虫来获取数据并从中提取出有价值的信息是至关重要的。当然爬虫还有很多重要的应用领域，以下列举了其中的一部分：
1. 搜索引擎
2. 新闻聚合
3. 社交应用
4. 舆情监控
5. 行业数据
####查看网站robort.txt文件
以淘宝网为例：
浏览器输入：www.taobao.com/robots.txt
```
User-agent:  Baiduspider
Allow:  /article
Allow:  /oshtml
Disallow:  /product/
Disallow:  /

User-Agent:  Googlebot
Allow:  /article
Allow:  /oshtml
Allow:  /product
Allow:  /spu
Allow:  /dianpu
Allow:  /oversea
Allow:  /list
Disallow:  /

User-agent:  Bingbot
Allow:  /article
Allow:  /oshtml
Allow:  /product
Allow:  /spu
Allow:  /dianpu
Allow:  /oversea
Allow:  /list
Disallow:  /

User-Agent:  360Spider
Allow:  /article
Allow:  /oshtml
Disallow:  /

User-Agent:  Yisouspider
Allow:  /article
Allow:  /oshtml
Disallow:  /

User-Agent:  Sogouspider
Allow:  /article
Allow:  /oshtml
Allow:  /product
Disallow:  /

User-Agent:  Yahoo!  Slurp
Allow:  /product
Allow:  /spu
Allow:  /dianpu
Allow:  /oversea
Allow:  /list
Disallow:  /

User-Agent:  *
Disallow:  /
```
注意：
上面robots.txt第一段的最后一行，通过设置“Disallow: /”禁止百度爬虫访问除了“Allow”规定页面外的其他所有页面。因此当你在百度搜索“淘宝”的时候，搜索结果下方会出现：“由于该网站的robots.txt文件存在限制指令（限制搜索引擎抓取），系统无法提供该页面的内容描述”。百度作为一个搜索引擎，至少在表面上遵守了淘宝网的robots.txt协议，所以用户不能从百度上搜索到淘宝内部的产品信息。

####相关工具
1.Chrome Developer Tools
2.postman
3.HTTPie
4.BuiltWith：识别网站使用的技术
```
>>>
>>> import builtwith
>>> builtwith.parse('http://www.bootcss.com/')
{'web-servers': ['Nginx'], 'font-scripts': ['Font Awesome'], 'javascript-frameworks': ['Lo-dash', 'Underscore.js', 'Vue.js', 'Zepto', 'jQuery'], 'web-frameworks': ['Twitter Bootstrap']}
>>>
>>> import ssl
>>> ssl._create_default_https_context = ssl._create_unverified_context
>>> builtwith.parse('https://www.jianshu.com/')
{'web-servers': ['Tengine'], 'web-frameworks': ['Twitter Bootstrap', 'Ruby on Rails'], 'programming-languages': ['Ruby']}
```
5.python-whois：查询网站的所有者
```
>>>
>>> import whois
>>> whois.whois('baidu.com')
{'domain_name': ['BAIDU.COM', 'baidu.com'], 'registrar': 'MarkMonitor, Inc.', 'whois_server': 'whois.markmonitor.com', 'referral_url': None, 'updated_date': [datetime.datetime(2017, 7, 28, 2, 36, 28), datetime.datetime(2017, 7, 27, 19, 36, 28)], 'creation_date': [datetime.datetime(1999, 10, 11, 11, 5, 17), datetime.datetime(1999, 10, 11, 4, 5, 17)], 'expiration_date': [datetime.datetime(2026, 10, 11, 11, 5, 17), datetime.datetime(2026, 10, 11, 0, 0)], 'name_servers': ['DNS.BAIDU.COM', 'NS2.BAIDU.COM', 'NS3.BAIDU.COM', 'NS4.BAIDU.COM', 'NS7.BAIDU.COM', 'dns.baidu.com', 'ns4.baidu.com', 'ns3.baidu.com', 'ns7.baidu.com', 'ns2.baidu.com'], 'status': ['clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited', 'clientTransferProhibited https://icann.org/epp#clientTransferProhibited', 'clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited', 'serverDeleteProhibited https://icann.org/epp#serverDeleteProhibited', 'serverTransferProhibited https://icann.org/epp#serverTransferProhibited', 'serverUpdateProhibited https://icann.org/epp#serverUpdateProhibited', 'clientUpdateProhibited (https://www.icann.org/epp#clientUpdateProhibited)', 'clientTransferProhibited (https://www.icann.org/epp#clientTransferProhibited)', 'clientDeleteProhibited (https://www.icann.org/epp#clientDeleteProhibited)', 'serverUpdateProhibited (https://www.icann.org/epp#serverUpdateProhibited)', 'serverTransferProhibited (https://www.icann.org/epp#serverTransferProhibited)', 'serverDeleteProhibited (https://www.icann.org/epp#serverDeleteProhibited)'], 'emails': ['abusecomplaints@markmonitor.com', 'whoisrelay@markmonitor.com'], 'dnssec': 'unsigned', 'name': None, 'org': 'Beijing Baidu Netcom Science Technology Co., Ltd.', 'address': None, 'city': None, 'state': 'Beijing', 'zipcode': None, 'country': 'CN'}
```
6.robotparser：解析robots.txt的工具
```
>>> from urllib import robotparser
>>> parser = robotparser.RobotFileParser()
>>> parser.set_url('https://www.taobao.com/robots.txt')
>>> parser.read()
>>> parser.can_fetch('Hellokitty', 'http://www.taobao.com/article')
False
>>> parser.can_fetch('Baiduspider', 'http://www.taobao.com/article')
True
>>> parser.can_fetch('Baiduspider', 'http://www.taobao.com/product')
False
```

####一个简单的爬虫
#####流程：
1.设定抓取目标（种子页面）并获取网页；
2.当服务器无法访问时，设置重试次数；
3.在需要的时候设置用户代理（否则无法访问页面）；
4.对获取的页面进行必要的解码操作；
5.通过正则表达式获取页面中的链接；
6.对链接进行进一步的处理（获取页面并重复上面的动作）；
7.将有用的信息进行持久化（以备后续的处理）

#####代码1（通过正则表达式查找内容）
```
import re
from urllib.error import URLError  # python2中为urllib.error2
from urllib.request import urlopen


def get_page_code(start_url, retry_times=3, charset='utf8'):
	try:
		# 获取页面
		html = urlopen(start-url).read().decode(charset)
	except URLError as ex:
		print('Error:', ex)
		if retry_times > 0:
			return get_page_code(start_url, retry_times - 1)
		else:
			return None
	return html


def main():
	html = get_page_code('http://sports.sohu.com/nba_a.shtml', charset='gbk')
	# 正则表达式获取url
	link_list = re.findall(r'<a[^>]+test=a\s[^>]*href=["\'](\S*)["\']', html)  

	for link in link_list:
		html = get_page_code(link)
		title_redex = re.compile(r'<h1>(.*)<span', re.IGNORECASE)
		title = re.findall(title_redex, html)[0]
		print(link)
		print(title)


if __name__ == '__main__':
    main()
	
```
#####代码2（通过CSS选择器语法查找内容）
```
import re
from bs4 import BeautifulSoup
import requests


def main():
    # 通过requests第三方库的get方法获取页面
    resp = requests.get('http://sports.sohu.com/nba_a.shtml')
    # 对响应的字节串(bytes)进行解码操作(搜狐的部分页面使用了gbk编码)
    html = resp.content.decode('gbk')
    # 创建BeautifulSoup对象来解析页面(相当于JavaScript的DOM)
    soup = BeautifulSoup(html, 'lxml')
    # 通过CSS选择器语法查找元素并通过循环进行处理
    for elem in soup.select('a[test=a]'):
        # 通过attrs属性(字典)获取元素的属性值
        link_url = elem.attrs['href']
        resp = requests.get(link_url)
        bs_sub = BeautifulSoup(resp.text, 'lxml')
        # print(bs_sub)
        # 使用正则表达式对获取的数据做经一步的处理
        print(link_url)
        print(re.sub(r'[\r\n]', '', bs_sub.select_one('h1').text))


if __name__ == '__main__':
    main()

```
#####代码3（作业：不断爬取页面中的相关新闻）
```
from urllib.error import URLError
from urllib.request import urlopen
from bs4 import BeautifulSoup


def get_page_code(start_url, retry_times=3, charsets=('utf-8', )):
    try:
        for charset in charsets:
            try:
                html = urlopen(start_url).read().decode(charset)
                break
            except UnicodeDecodeError:
                html = None
    except URLError as ex:
        print('Error:', ex)
        if retry_times > 0:
            return get_page_code(start_url, retry_times=retry_times - 1, charsets=charsets)
        else:
            return None

    return html


def main():
    url_list = ['http://news.sohu.com/20171226/n526348972.shtml']
    visited_list = set({})
    while len(url_list) > 0:
        current_url = url_list.pop(0)
        visited_list.add(current_url)
        html = get_page_code(current_url, charsets=('utf-8', 'gbk', 'gb2312'))
        if html:
            soup = BeautifulSoup(html, 'lxml')  # 创建BeautifulSoup对象解析页面
            link_lists = soup.select('div[class="mutu-news"] ul li a')  # 获取相关新闻a标签
            title = soup.select('h1[itemprop="headline"]')  # 获取页面标题

            print(title)
            print(current_url)

            for link in link_lists:
                link_url = link.attrs['href']  # 获取相关新闻url
                url_list.append(link_url)  # 将相关新闻url添加进url_list
                # print(link)
                # print(link.text)
                # print(link_url)


if __name__ == '__main__':
    main()
```