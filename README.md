# Qunar\_Selenium自动化爬虫

## 目标： 自动化爬取北京当天的酒店信息，并存为文本。

### 1. 自动输入地点和入住时间，点击搜索按钮
### 2. 获取一页完整数据，用Selenium操作javascript下拉窗口，完成数据加载。
### 3. 获取渲染后的结果，用BeautifulSoup提取酒店信息并存储
### 4. 解析完成后，自动点击下一页，继续抽取数据。


#### 演示结果如下
![](https://github.com/sumerzhang/Qunar_Selenium/blob/master/qunar_selenium.gif)

### 首先找到搜索界面，用Chrome审查元素读取元素的位置，通过Selenium自动填充数据和点击搜索按钮。
```python
		ele_toCity = driver.find_element_by_name("toCity")
		ele_fromDate = driver.find_element_by_id("fromDate")
		ele_toDate = driver.find_element_by_id("toDate")
		ele_search = driver.find_element_by_class_name('search-btn')
		ele_toCity.clear()
		ele_toCity.send_keys(to_city)
		ele_toCity.click()
		ele_fromDate.clear()
		ele_fromDate.send_keys(fromdate)
		ele_toDate.clear()
		ele_toDate.send_keys(todate)
		ele_search.click()
		page_num = 0

```

### 第二步使用Selenium执行javascirpt代码，把网页拖到底部，完成页面加载

```python
	while True:
			try:
				WebDriverWait(driver, 10).until(
					EC.title_contains(to_city))
			except Exception as e:
				print(e)
				break
			time.sleep(5)
			
			js = "window.scrollTo(0, document.body.scrollHeight);"
			driver.execute_script(js)
			time.sleep(5)
			
			html_const = driver.page_source
```

### 第三步：使用BeautifulSoup解析酒店数据，清洗并存储。
```python
		soup = BeautifulSoup(html_const, 'lxml')
			infos = soup.find_all(class_ = 'item_hotel_info')
			f = codecs.open(to_city+fromdate + u'.html', 'a', 'utf-8')
			
			for info in infos:
				f.write(str(page_num)+ '--'* 50)
				content = info.get_text().replace(' ', '').replace('\t', '').strip()
				for line in [ln for ln in content.splitlines() if ln.strip()]:
					f.write(line)
					f.write('\r\n')
					
			f.close()

```

### 第四步，点击下一页，继续重复上述过程
```python
	try:
				next_page = WebDriverWait(driver, 10).until(
					EC.visibility_of(driver.find_element_by_css_selector(".item.next")))
						
				next_page.click()
				page_num += 1
				time.sleep(10)
			except Exception as e:
				print(e)
				break

```

详细的过程请查看源码

感谢您的时间 (~.~)



