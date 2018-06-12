###模拟登录
网页登录方法一：
```
import requests
from bs4 import BeautifulSoup

def main():
	resp = requests.get('http://github.com/login')
	if resp.tatus.code != 200:
		return
	# 获取cookies
	cookies = resp.cookies.get_dict()
	soup = BeautifulSoup(resp.text, 'lxml')
	# 获取第一个隐藏域
	utf8_value = soup.select_one('form input[name=utf8]').attrs['value']
	# 获取第二个隐藏域
	authenticity_token_value = soup.select_one('form input[name=authenticity_token]').attrs['value']

	data = {
		'utf8': utf8_value,
        'authenticity_token_value': authenticity_token_value,
        'login': 'Kai119394',
        'password': 'qu19931202kai'
	}

	resp = requests.post('http://github.com/session', data=data, cookies=cookies)
	print(resp.text)

if __name__ == '__main__':
	main()
```

网页登录方法二：
```
import robobrowser

def main():
	# 创建浏览器对象
	b = robobrowser.RoboBrowser(parser='lxml')
	# 打开网页
	b.open('http://github.com/login')
	# 获取session
	f = b.get_form(action='/session')
	
	f['login'].value = '登录账号'
	f['password'].value = '登录密码'
	# 提交表单
	b.submit_form(f)
	for a_tag in b.select('a[href]'):
		print(a_tag.attrs['href'])
	
if __name__ == '__main__':
    main()
```

###驱动浏览器
注意：需下载安装相应浏览器的驱动软件，并添加环境变量
```
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

def mian():
	
	# 驱动谷歌服务器
	driver = webdriver.Chrome()
	driver.get('https://v.taobao.com/v/content/live?catetype=704&from=taonvlang')

	# 模拟鼠标点击，键盘输入事件
	elem = driver.find_element_by_css_selector('input[placeholder="输入关键词搜索"]')
	elem.send_keys('美女')
	elem.send_keys(Keys.ENTER)

	soup = BeautifulSoup(driver.page_source, 'lxml')
	for imag_tag in soup.body.select('img[src]'):
		print(img_tag.attrs[''src])

if __name__ == '__main__':
    main()
```

###图片处理
```
import base64
from PIL import Image, ImageFilter
from pytesseract import image_to_string
import requests
from io import BytesIO


def main():
	# 给图片加滤镜
    guido_img = Image.open(open('guido.jpg', 'rb'))
    guido2_img = guido_img.filter(ImageFilter.GaussianBlur)
    guido2_img.save(open('guido2.jpg', 'wb'))
	
	# 调整图片对比度
    img1 = Image.open(open('tesseract.png', 'rb'))
    img2 = img1.point(lambda x: 0 if x < 128 else 255)
    img2.save(open('tesseract2.png', 'wb'))

    print(image_to_string(img2))

    resp = requests.get('https://pin2.aliyun.com/get_img?type=150_40&identity=mailsso.mxhichina.com&sessionid=k0xHyBxU3K3dGXb59mP9cdeTXxL9gLHSTKhRZCryHxpOoyk4lAVuJhgw==')
    # BytesIO实现在内存中读写bytes
    img3 = Image.open(BytesIO(resp.content))
    img3.save('captcha.jpg')

	# image_to_string验证码识别方法
    print(image_to_string(img3))
    print(base64.b64encode(resp.content))


if __name__ == '__main__':
    main()
```