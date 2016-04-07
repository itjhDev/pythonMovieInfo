# Python抓取电影天堂电影信息
 
最近想做一个电影资讯的App，由于豆瓣不对外开发API了，正愁怎么录入数据呢。最近在学`Python` 想想那就用它来抓取电影信息吧。

<font color="red">此项目仅供学习</font>

> Python2.7
> 
> Mac OS

抓取的是电影天堂里面最新电影的页面。链接地址：`http://www.dytt8.net/html/gndy/dyzz/index.html`


#### 获取页面的中电影详情页链接

```python
import urllib2
import os
import re
import string


# 电影URL集合
movieUrls = []


# 获取电影列表
def queryMovieList():

	url = 'http://www.dytt8.net/html/gndy/dyzz/index.html'   
	conent = urllib2.urlopen(url)
	conent =  conent.read()
	conent = conent.decode('gb2312','ignore').encode('utf-8','ignore')  
	pattern = re.compile ('<div class="title_all"><h1><font color=#008800>.*?</a>></font></h1></div>'+
	                      '(.*?)<td height="25" align="center" bgcolor="#F4FAE2"> ',re.S)
	items = re.findall(pattern,conent) 
	
	str = ''.join(items)
	pattern = re.compile ('<a href="(.*?)" class="ulink">(.*?)</a>.*?<td colspan.*?>(.*?)</td>',re.S)
	news = re.findall(pattern, str)

	for  j in news:
	   
        	movieUrls.append('http://www.dytt8.net'+j[0])

```

#### 抓取详情页中的电影数据

```python
def queryMovieInfo(movieUrls):

	for index, item in enumerate(movieUrls):

		print('电影URL: ' + item)

		conent = urllib2.urlopen(item)
		conent = conent.read()
		conent = conent.decode('gb2312','ignore').encode('utf-8','ignore')  


		movieName = re.findall(r'<div class="title_all"><h1><font color=#07519a>(.*?)</font></h1></div>', conent, re.S)
		if (len(movieName) > 0):
			movieName = movieName[0] + ""
			# 截取名称
			movieName = movieName[movieName.find("《") + 3:movieName.find("》")]
		else:
			movieName = ""

		print("电影名称: " + movieName.strip())

		movieContent = re.findall(r'<div class="co_content8">(.*?)</tbody>',conent , re.S)


		pattern = re.compile('<ul>(.*?)<tr>', re.S)
		movieDate = re.findall(pattern,movieContent[0])

		if (len(movieDate) > 0):
			movieDate = movieDate[0].strip() + ''
		else:
			movieDate = ""

		print("电影发布时间: " + movieDate[-10:])

		pattern = re.compile('<br /><br />(.*?)<br /><br /><img')
		movieInfo = re.findall(pattern, movieContent[0])

		if (len(movieInfo) > 0):
			movieInfo = movieInfo[0]+''

			# 删除<br />标签
			movieInfo = movieInfo.replace("<br />","")

			# 根据 ◎ 符号拆分

			movieInfo = movieInfo.split('◎')

		else:
			movieInfo = ""

		print("电影基础信息: ")

		for item in movieInfo:
			print(item)


		# 电影海报
		pattern = re.compile('<img.*? src="(.*?)".*? />', re.S)		
		movieImg = re.findall(pattern,movieContent[0])

		if (len(movieImg) > 0):
			movieImg = movieImg[0]
		else:
			movieImg = ""
 
		print("电影海报: " + movieImg)

		pattern = re.compile('<td style="WORD-WRAP: break-word" bgcolor="#fdfddf"><a href="(.*?)">.*?</a></td>', re.S)
		movieDownUrl = re.findall(pattern,movieContent[0])

		if (len(movieDownUrl) > 0):
			movieDownUrl = movieDownUrl[0]
		else:
			movieDownUrl = ""

		print("电影下载地址：" + movieDownUrl + "")

		print("------------------------------------------------\n\n\n")
	
```

#### 执行抓取

```python

if __name__=='__main__':


     print("开始抓取电影数据");
	 
     queryMovieList()

     print(len(movieUrls))


     queryMovieInfo(movieUrls)

     print("结束抓取电影数据")

```

#### 总结

学好正则表达式很重要,很重要,很重要!!!! Python的语法好有感觉, 对比Java ...


