---
title: urllib和BeautifulSoup使用指南
tag: [Python,urllib,BeautifulSoup,pymysql]
category: Python
excerpt: 爬取天眼查的公司评分
cover: /EVA2.jpg
---
### 自己遇到的坑点
`\<div class = "search-block header-block-container">......\</div>`
不能用soup.select(search-block header-block-container)选择出来，
因为这是两个并列的类名，要用soup.select(.search-block.header-block-container)才能选择出来

### urllib
``` bash
# 转化为机器可以识别带中文的网址，编码类型为unicode。只转换汉字部分，不能全部网址进行转换
company = parse.quote(name)
url = "https://www.tianyancha.com/search?key=" + company

# 浏览器伪装池，将爬虫伪装成浏览器，避免被网站屏蔽
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.87 Safari/537.36",
    # 手动登录天眼查然后拷贝cookie
    "Cookie": "XXXX"
}
response = request.urlopen(req)
# 得到字符串形式的html文本
html = response.read().decode('utf-8')
            
```
### BeautifulSoup
[详细教程](https://blog.csdn.net/slhlde/article/details/81937838)

```bash
# 创建BeautifulSoup对象，html解析器有html.parser、lxml、html5lib
soup = BeautifulSoup(html, "html.parser")
```
