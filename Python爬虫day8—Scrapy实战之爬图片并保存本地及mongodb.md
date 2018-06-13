###scrapy实战之网页动态加载
####一、首先创建项目
```
scrapy startproject image360
```
####二、然后创建爬虫
```
scrapy genspider image image.so.com
```
####三、定义item
使用pycharm打开项目，定义ittem.py文件
```
import scrapy

class BeautyItem(scrapy.Item):
    title = scrapy.Field()
    tag = scrapy.Field()
    width = scrapy.Field()
    height = scrapy.Field()
    url = scrapy.Field()
```
####四、编辑image蜘蛛文件
进入spider目录，编写爬虫。
* 动态网页获取url：

![这里写图片描述](https://img-blog.csdn.net/20180609142609127?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc4MjA1MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

* 复制request url，在浏览器中打开获取到的是json数据：

![这里写图片描述](https://img-blog.csdn.net/20180609142807366?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc4MjA1MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

* 格式化查看：

![这里写图片描述](https://img-blog.csdn.net/20180609142950203?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc4MjA1MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
class ImageSpider(scrapy.Spider):
	name = 'image'
	allowed_domains = ['image.so.com']

	def start_requests(self):
		base_url = 'http://image.so.com/zj?'
		param = {'ch': 'beauty', 'listtype': 'new', 'temp': 1}
		for page in range(10):
			param['sn'] = page * 30
			full_url = base_url + urlencode(param)
            yield scrapy.Request(url=full_url, callback=self.parse)

    def parse(self, response):  # 解析网页，获取数据
        model_dict = loads(response.text)
        for elem in model_dict['list']:
            item = BeautyItem()  # 实例化item
            item['title'] = elem['group_title']
            item['tag'] = elem['tag']
            item['width'] = elem['cover_width']
            item['height'] = elem['cover_height']
            item['url'] = elem['qhimg_url']
            yield item
```
####五、编辑pipelines，保存数据到本地
* 将图片保存到本地
打开pipeline.py进行中间件编写，这里的话主要继承了scrapy的：ImagesPipeline这个类，我们需要在里面实现：def get_media_requests(self, item, info)这个方法。
```
class SaveImagePipeline(ImagesPipeline):

    def get_media_requests(self, item, info):
        yield Request(url=item['url'])

    def item_completed(self, results, item, info):
        if not results[0][0]:
            raise DropItem('下载失败')
        return item

    def file_path(self, request, response=None, info=None):
        return request.url.split('/')[-1]
```
* setting设置
```
#图片存储位置
IMAGES_STORE = './resources/'
#启动图片下载中间件
ITEM_PIPELINES = {  'image360.pipelines.SaveImagePipeline': 300,
}
```
####六、启动爬虫，即可将爬到的图片保存到本地
```
scrapy crawl image
```

####七、连接mongodb并保存数据
* 编辑pipelianes
```
class SaveToMongoPipeline(object):

	def __init__(self, mongo_url, mongo_db):
		self.mongo_url = mongo_url
		self.mongo_db = mongo_db

	def process_item(self, item, spider)
		data = [{
			'title': item['title'],
            'url': item['url']
		}]
		self.db.image.insert(data)
		return item

	def open_spider(self, spider):
		self.client = pymongo.MongoClient(self.mongo_url)
		self.db = self.client[self.mongo_db]

	def close_spider(self):
		self.client.close()
	
	@classmethod
	def from_crawler(cls, crawler):
		return cls(
			mongo_url = crawler.setting.get('MONGO_URL'),
			mongo_db = crawler.setting.get('MONGO_DB')
		)
```
* 设置setting
```
ITEM_PIPELINES = {
	'image360.pipelines.SaveToMongoPipeline': 301,
}

MONGO_URL = 'mongodb://<ip地址>:<端口号>'
MONGO_DB = '<数据库名>'
```