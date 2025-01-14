<nav id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgheadline1">1. 到底该不该管js代码</a></li>
<li><a href="#orgheadline5">2. selenium模块</a>
<ul>
<li><a href="#orgheadline2">2.1. 安装</a></li>
<li><a href="#orgheadline3">2.2. 一个简单例子</a></li>
<li><a href="#orgheadline4">2.3. 选中某个标签元素</a></li>
</ul>
</li>
<li><a href="#orgheadline6">3. 后台虚拟窗口运行</a></li>
</ul>
</div>
</nav>


# 到底该不该管js代码<a id="orgheadline1"></a>

把这个问题先提出来就是要提醒读者，只有确定你要抓取的内容很重要而且必须把js展开内容才能获取，那么才考虑展开js代码。这倒不是实现上有多困难，而是成本太昂贵了。如下面所述及的，我们需要建立一种虚拟后台显示窗口，然后在这个窗口内运行如FireFox这样的浏览器。读者可以看到这种展开工作成本是极其昂贵的。所以如果你的程序一旦决定具体展开js代码了，那么你要清楚决定你程序性能的短板就在这里了，很多常规的程序优化技术和手段可能反倒是杯水车薪了，而围绕着这个具体展开，尽量减少具体的展开工作量，就成了程序性能优化的关键了。

# selenium模块<a id="orgheadline5"></a>

selenium模块的官方文档在 [这里](http://selenium-python.readthedocs.org/index.html) 。下面就模块的最核心内容做出一些说明。

## 安装<a id="orgheadline2"></a>

    sudo pip3 install selenium

## 一个简单例子<a id="orgheadline3"></a>

```python
from selenium import webdriver

browser = webdriver.Firefox()
browser.get('http://www.163.com')

html = browser.page_source
print(html)
print(browser.title)
print(browser.current_url)
browser.quit()
```

这里调用的是火狐后台 `webdriver.Firefox` ，你还可以调用其他后台，这里略过讨论了。

通过browser的 `get` 方法来在模拟浏览器中输入一个网址，然后通过 `page_source` 方法来获取目标网址源码， browser还有属性 `title` 当前网页标题； `current_url` 当前网址。然后通过 `quit` 方法关闭该浏览器。

一般情况下获取目标网页的html解析之后的源码就可以了，通过其他工具如beautifulsoup等来进一步提取信息。selenium的browser还有很多方法来获取标签对象，某一两个信息提取任务的时候可以用一下，其主要是用于选中某个标签元素，然后进行额外的模拟人手工的如点击输入文本等动作的。

## 选中某个标签元素<a id="orgheadline4"></a>

前面提及的browser对象有很多选中某个或某些标签元素的方法:

-   find\_element\_by\_id
-   find\_element\_by\_name
-   find\_element\_by\_xpath
-   find\_element\_by\_link\_text
-   find\_element\_by\_partial\_link\_text
-   find\_element\_by\_tag\_name
-   find\_element\_by\_class\_name
-   find\_element\_by\_css\_selector

然后这些方法若element改为elements则会选中多个标签元素，如: `find_elements_by_id` 。

具体请参看官方文档，这里就不一一说明了。 [这篇网页](https://saucelabs.com/resources/selenium/css-selectors) 介绍了Xpath和css选定的一些细节，介绍的很好。

这些函数返回的是标签元素对象e，你可以通过 `e.get_attribute('src')` 来获取该标签的某个属性的值。然后还有其他一些方法，这里先略过讨论了。正如前所述及的，selenium的重点是模拟浏览器的各个动作，而不是要做类似beautifulsoup这样的api工作。所以其提供了 `e.click()` 就模拟了鼠标点击在该标签元素上的行为；还有 `e.send_keys(Keys.RETURN)` 就模拟了在该标签元素上按下回车键的行为。等等这些才是selenium的关注重点，更多相关细节请参看官方文档的相关 Action Chains 部分。

# 后台虚拟窗口运行<a id="orgheadline6"></a>

经过测试行为一切OK了，你就不希望再看到弹出的浏览器窗口了，你可以通过一种虚拟显示技术来做到。首先安装 `xvfb`:

    sudo apt-get install xvfb

然后安装 `pyvirtualdisplay` 模块:

    sudo pip3 install pyvirtualdisplay

这里参考了 [这篇网页](http://coreygoldberg.blogspot.com/2011/06/python-headless-selenium-webdriver.html) 。大体例子如其所示:

    def wget_forcejs(url):
        from pyvirtualdisplay import Display
        from selenium import webdriver
    
        display = Display(visible=0, size=(800, 600))
        display.start()
    
        # now Firefox will run in a virtual display.
        # you will not see the browser.
        browser = webdriver.Firefox()
        browser.get(url)
    
        html = browser.page_source
        print(browser.title)
    
        browser.quit()
        display.stop()
        return html