## scrapy 快速入门

[官网文档](https://docs.scrapy.org/)

### 安装

```bash
pip install scrapy -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### 创建项目

以创建 `tutorial` 项目为例：

```bash
scrapy startproject tutorial
```

项目结构如下：

```bash
tutorial/
    scrapy.cfg            # deploy configuration file

    tutorial/             # project's Python module, you'll import your code from here
        __init__.py

        items.py          # project items definition file

        middlewares.py    # project middlewares file

        pipelines.py      # project pipelines file

        settings.py       # project settings file

        spiders/          # a directory where you'll later put your spiders
            __init__.py
```

### 添加目标

```bash
# 进入刚刚创建的项目
cd tutorial

# 继续进入spiders目录，注意这里的tutorial是项目内的目录名
cd tutorial/spiders

# 添加文件
vi quotes_spider.py 

# 按 i 进入编辑模式后复制粘贴以下代码
```

```python
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        page = response.url.split("/")[-2]
        filename = 'quotes-%s.html' % page
        with open(filename, 'wb') as f:
            f.write(response.body)
        self.log('Saved file %s' % filename)
```

### 开始爬虫

输入以下命令：

```bash
scrapy crawl quotes
```

即可看到控制台很多输出，同时也可以查看刚刚项目爬到的数据写入的文件 `quotes-1.html`

到此为止即可认为第一个爬虫项目已经成功了，尽管爬取的是一个基本上没有意义的html文件。

### 关于XPath

XPath 是一门在 XML 文档中查找信息的语言。语法极其简单，推荐参考 [菜鸟教程](https://www.runoob.com/xpath/xpath-tutorial.html)。

接下来的爬虫都将使用 `XPath` 语言进行抓取网页中需要的内容。

推荐使用谷歌浏览器安装XPath插件，方便测试XPath语句是否有效。

### 简单实例（1）

目标：爬取网页 [宋词精选](https://www.gushiwen.org/gushi/songci.aspx) 上的所有指向诗词详细内容的URL。

XPath：`//div[@class="typecont"]/span/a/@href`

具体 Spider 代码（在上面实例中进行少量更改）：

```python
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"
    def start_requests(self):
        url = "https://www.gushiwen.org/gushi/songci.aspx"
        yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        results = response.xpath('//div[@class="typecont"]/span/a/@href')
        urls = []
        for result in results:
            urls.append(result.get())
        print(urls)

```

### 简单实例（2）

目标：经过实例1已经拿到宋词详细内容的URL，接着根据这些URL爬取具体数据。

item:  名称；时期；作者；上下阙；

XPath：

* 名称：`//h1`
* 时期：`//p[1][@class="source"]/a[1]`
* 作者：`//p[1][@class="source"]//a[2]`
* 上下阙：`//div[@class="contson"]`

需要编辑 `items.py` 文件，添加一些属性，添加后结果如下：

```python
import scrapy

# 获得所有的URL和Name
class TutorialItem(scrapy.Item):
    # define the fields for your item here like:
    # 词名（含词牌名）
    name = scrapy.Field()
    # 时期
    dynasty = scrapy.Field()
    # 作者
    author = scrapy.Field()
    # 内容
    content = scrapy.Field()     
```

编辑那个Spider文件如下：

```python
import scrapy
from tutorial.items import TutorialItem 
from tqdm import tqdm
import json
import re

class QuotesSpider(scrapy.Spider):
    name = "quotes"
    
    def start_requests(self):
        url = "https://www.gushiwen.org/gushi/songci.aspx"
        yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        # page = response.url.split("/")[-2]
        results = response.xpath('//div[@class="typecont"]/span/a/@href')
        urls = []
        for result in results:
            urls.append(result.get())

        # for each url
        with tqdm(total=len(urls)) as pbar:
            for url in urls:
                yield scrapy.Request(url=url, callback=self.parse2)
                pbar.update(1)

	# 读取宋词内容
    def parse2(self, response):
        item = TutorialItem()
        # 包括名称，朝代，作者，内容四部分
        item['name'] = response.xpath('//h1/text()').get()
        item['dynasty'] = response.xpath('//p[1][@class="source"]/a[1]/text()').get()
        item['author'] = response.xpath('//p[1][@class="source"]/a[2]/text()').get()
        # 对内容部分做一定的处理：保留<p>标签和<br>标签，分别替换为p与br,去除其他html标签。
        content = str(response.xpath('//div[@class="contson"]').get()).replace('<br>','br').replace('<br/>','br').replace('<p>','p').replace('\n','')
        dr = re.compile(r'<[^>]+>',re.S)
        content = dr.sub('',content)
        item['content'] = content
        yield item
```

需要注意需要配置编码格式，找到项目中的 `setting.py` 文件，添加一行内容如下：

```bash
FEED_EXPORT_ENCODING = 'utf-8'
```

运行项目和上面有所不同，需要把管道(pipline)中的数据导出到json文件中，输入命令如下：

```bash
scrapy crawl quotes -o quotes.json
```

运行完成后可以得到一个224行的json文件，去除首行与尾行，总共有222首词收录其中。

### 实例3

继续上面的例子，把数据写入MySQL中。

首先修改配置文件 `setting.py`，开启pipline。找到配置文件中如下内容，去掉前面的注释符号：

```bash
ITEM_PIPELINES = {
    'tutorial.pipelines.TutorialPipeline': 300,
}
```

因为需要访问MySQL，所以需要安装相应的依赖：

```bash
pip install mysql-connector -i https://pypi.tuna.tsinghua.edu.cn/simple
```

编辑 TutorialPipeline.py，代码如下：

```python

import mysql.connector

class TutorialPipeline(object):
    def __init__(self):
        self.mydb = mysql.connector.connect(
          host="smileyan.cn",
          port=3306,
          user="root",
          passwd="密码",
          database="songci"
         )
        
    def process_item(self, item, spider):
        print('@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@')
        mycursor = self.mydb.cursor()
        sql = "INSERT INTO content(name, author, dynasty, content) VALUES (%s, %s, %s, %s)"
        val = (str(item['name']), str(item['author']), str(item['dynasty']), str(item['content']))
        mycursor.execute(sql, val)
        self.mydb.commit() 
        return item

```

执行后，查看数据库，就可以看到已经写入成功。

```python
import scrapy
from tutorial.items import TutorialItem 
from tqdm import tqdm
import json
import re

class QuotesSpider(scrapy.Spider):
    name = "quotes"
    def __init__(self):
        self.item = TutorialItem()
    def start_requests(self):
        url = "https://www.gushiwen.org/gushi/songci.aspx"
        yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        # page = response.url.split("/")[-2]
        results = response.xpath('//div[@class="typecont"]/span/a/@href')
        urls = []
        for result in results:
            urls.append(result.get())

        # for each url
        with tqdm(total=len(urls)) as pbar:
            for url in urls:
                yield scrapy.Request(url=url, callback=self.parse2)
                pbar.update(1)

    def parse2(self, response):
        self.item['name'] = response.xpath('//h1/text()').get()
        self.item['dynasty'] = response.xpath('//p[1][@class="source"]/a[1]/text()').get()
        self.item['author'] = response.xpath('//p[1][@class="source"]/a[2]/text()').get()
        content = str(response.xpath('//div[@class="contson"]').get()).replace('<br>','br').replace('<br/>','br').replace('<p>','p').replace('\n','')
        dr = re.compile(r'<[^>]+>',re.S)
        content = dr.sub('',content)
        self.item['content'] = content
        self.item['name'] = response.xpath('//h1/text()').get()
        # 翻译
        xpath_fangyi = "//div[@class='sons'][2]/div[@class='contyishang']"
        self.item['explanations'] = response.xpath(xpath_fangyi).get()
        # 获得翻译 ID
        fanyiIdTemp = self.item['explanations'].split("fanyiShow")
        # 如果被隐藏了，则需要进一步获得数据
        if(len(fanyiIdTemp)>=2):
            fanyiId = fanyiIdTemp[1]
            fanyiId = fanyiId.split('\'')[1]
            self.item['explanations'] = fanyiId
            url = "https://so.gushiwen.org/nocdn/ajaxfanyi.aspx?id="+fanyiId
            yield scrapy.Request(url=url, callback=self.parse3)
        else:
            # 对翻译和注释进行处理
            body = response.xpath("//div[@class='contyishang']/p[1]").get()
            self.item['explanations'] = body
            body = response.xpath("//div[@class='contyishang']/p[2]").get()
            self.item['notes']=body
            yield self.item
    def parse3(self, response):
        body = response.xpath("//div[@class='contyishang']/p[1]").get()
        self.item['explanations'] = body
        body = response.xpath("//div[@class='contyishang']/p[2]").get()
        self.item['notes']=body
        yield self.item
        
```







```python
        # 翻译
#         xpath_fangyi = "//div[@class='sons'][2]/div[@class='contyishang']"
#         self.item['explanation'] = response.xpath(xpath_fangyi).get()
        # 获得翻译 ID
#         fanyiIdTemp = self.item['explanation'].split("fanyiShow")
        # 如果被隐藏了，则需要进一步获得数据
#         if(len(fanyiIdTemp)>=2):
#             fanyiId = fanyiIdTemp[1]
#             fanyiId = fanyiId.split('\'')[1]
#             self.item['explanation'] = fanyiId
#             url = "https://so.gushiwen.org/nocdn/ajaxfanyi.aspx?id="+fanyiId
#             yield scrapy.Request(url=url, callback=self.parse3)
#         else:
#             # 对翻译和注释进行处理
#             body = response.xpath("//div[@class='contyishang']/p[1]").get()
#             self.item['explanation'] = body
#             body = response.xpath("//div[@class='contyishang']/p[2]").get()
#             self.item['note']=body
        
```

