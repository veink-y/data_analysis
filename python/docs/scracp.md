
### Header 

```
headers = {'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36'}
```



### 解析方式

 	1. 直接处理
 	2. json解析
 	3. 正则表达式
 	4. BeautifulSoup
 	5. PyQuery
 	6. Xpath



### JS渲染问题

1. 分析Ajax请求
2. selenium.WebDriver

```python
from selenium import webdriver
driver = webdriver.Chrome()
driver.get("http://www.baidu.com")
print(driver.page_source)
```

3. Splash
4. PyV8、Ghost.py



### 保存数据

1. 文本

2. 关系型数据库
3. 非关系型数据库
4. 二进制文件