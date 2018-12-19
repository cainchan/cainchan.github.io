---
title: selenium PhantomJS自动登录网站
date: 2018-12-20 00:30:45
tags: 
 - Selenium
 - PhantomJs
---
```python
#!/usr/bin/env python
#coding:utf-8
import re
import sys
import time
import urllib2

from selenium import webdriver
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities
from selenium.webdriver.support import ui

#设置user-agent
dcap = dict(DesiredCapabilities.PHANTOMJS)
dcap["phantomjs.page.settings.userAgent"] = ("Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:25.0) Gecko/20100101 Firefox/25.0 ")

#设置参数
service_args = ['--proxy=192.168.1.201:7070','--proxy-type=socks5','--ignore-ssl-errors=true']
driver = webdriver.PhantomJS(executable_path='/root/phantomjs2.1.1/bin/phantomjs', desired_capabilities=dcap,service_args=[])
driver.maximize_window()
wait = ui.WebDriverWait(driver, 10)

#登录alivv
url = "http://www.alivv.com"
driver.get(url)
wait.until(lambda dr: dr.find_element_by_css_selector(".login_login").is_displayed())
driver.find_element_by_css_selector(".login_login").click()
driver.find_element_by_css_selector("#email").send_keys("kaychen")
driver.find_element_by_css_selector("#pass").send_keys("0516*****")
driver.find_element_by_css_selector("#jzpass").click()
driver.find_element_by_css_selector("#submitFormBtn").click()
#进入会员中心
driver.find_element_by_css_selector("#sname a").click()
#输出页面源码
print driver.page_source
time.sleep(3)
driver.save_screenshot('/tmp/screen.png')
driver.quit()
```