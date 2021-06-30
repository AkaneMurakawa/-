# Selenium Python

## 起步

注：需下载chromedriver.exe ，需对应版本：[https://www.newbe.pro/Mirrors/Mirrors-ChromeDriver/](https://www.newbe.pro/Mirrors/Mirrors-ChromeDriver/)

示例：

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time

# 环境地址
URL = "http://baidu.com"

def start(driver):
    # 访问环境地址
    driver.get(URL)
     # 刷新浏览器
    driver.refresh()

if __name__ == "__main__":
    # 将文件放在driver路径下，相对路径
    driver = webdriver.Chrome("./driver/chromedriver.exe")
    start(driver)
    driver.close
```

## 元素定位

[http://www.testclass.net/selenium\_python/find-element](http://www.testclass.net/selenium_python/find-element)

* find\_element\_by\_id\(\)  根据id 【常用】
* find\_element\_by\_name\(\) 根据名称 【常用】
* find\_element\_by\_class\_name\(\) 根据class名称
* find\_element\_by\_tag\_name\(\) 根据标签名称，例如：input 【常用】
* find\_element\_by\_link\_text\(\) 根据链接文本
* find\_element\_by\_partial\_link\_text\(\) 根据部分链接文本
* find\_element\_by\_xpath\(\) 使用路径表达式 【必学】
* find\_element\_by\_css\_selector\(\)

```python
dr.find_element_by_tag_name("input")

dr.find_element_by_xpath("//*[@id='kw']")
```

## 常用方法

```python
driver.get("http://m.baidu.com") # 请求
driver.set_window_size(480, 800) # 设置窗口大小
driver.maximize_window() # 浏览器最大化
driver.back() # 返回
driver.forward() # 前进
driver.refresh() # 刷新
driver.close() # 关闭当前窗口
driver.quit() # 关闭所有窗口


driver.find_element_by_id("kw").clear() # 清除文本
driver.find_element_by_id("kw").send_keys("selenium") # 输入文本
driver.find_element_by_id("cp").text # 获取文本内容
driver.find_element_by_id("kw").get_attribute('type') # 获取指定属性
driver.find_element_by_xpath("//tr/th[1]/label/input").click() # 点击
driver.find_element_by_name("file").send_keys('D:\\upload_file.txt') # 文件上传
driver.find_element_by_id('kw').submit() # 提交


driver.find_element_by_id("kw").send_keys(Keys.CONTROL, 'a') # ctrl+a 全选输入框内容
driver.find_element_by_id("kw").send_keys(Keys.CONTROL, 'x') # ctrl+x 剪切输入框内容
driver.find_element_by_id("kw").send_keys(Keys.CONTROL, 'v') # ctrl+v 粘贴内容到输入框
driver.get_screenshot_as_file("D:\\baidu_img.jpg") # 截图

driver.execute_script(js)# 执行JS

# 其他
time.sleep(5)
```

小结：

使用时，调用driver相应的属性或者方法即可。重点在于定位到元素。

## 使用示例

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time

# 环境地址
URL = "http://baidu.com"
# 用户名
USERNAME = "admin"
# 密码
PASSWORD = "123456"

def start(driver):
    # 浏览器最大化
    driver.maximize_window()

    # 访问环境地址
    driver.get(URL)
    time.sleep(1)


    # 填写账号和密码
    usernameElement = driver.find_element_by_id("username")
    usernameElement.send_keys(USERNAME)
    passwordElement = driver.find_element_by_id("password")
    passwordElement.send_keys(PASSWORD)
    time.sleep(2)

    # 登录
    loginElement = driver.find_element_by_id("sign")
    # 点击
    loginElement.click() 
    time.sleep(5)

     # 刷新浏览器
    driver.refresh()

if __name__ == "__main__":
    driver = webdriver.Chrome("./driver/chromedriver.exe")
    start(driver)
    driver.close
```

