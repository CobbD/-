<nav id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgheadline1">1. 前言</a></li>
<li><a href="#orgheadline8">2. 一些命令</a>
<ul>
<li><a href="#orgheadline2">2.1. 新建一个项目</a></li>
<li><a href="#orgheadline3">2.2. 新建一个app</a></li>
<li><a href="#orgheadline4">2.3. 数据库操作</a></li>
<li><a href="#orgheadline5">2.4. 交互式环境</a></li>
<li><a href="#orgheadline6">2.5. 创建超级用户</a></li>
<li><a href="#orgheadline7">2.6. 运行网络服务器</a></li>
</ul>
</li>
<li><a href="#orgheadline10">3. url分发</a>
<ul>
<li><a href="#orgheadline9">3.1. 获取full-url</a></li>
</ul>
</li>
<li><a href="#orgheadline17">4. 模型</a>
<ul>
<li><a href="#orgheadline11">4.1. 默认创建的表格</a></li>
<li><a href="#orgheadline12">4.2. 字段类型</a></li>
<li><a href="#orgheadline13">4.3. 通用可选项</a></li>
<li><a href="#orgheadline14">4.4. 数据库中的关系</a></li>
<li><a href="#orgheadline16">4.5. ORM层的使用方法</a>
<ul>
<li><a href="#orgheadline15">4.5.1. 去重</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#orgheadline18">5. 配置</a></li>
<li><a href="#orgheadline19">6. 国际化</a></li>
<li><a href="#orgheadline21">7. 表单</a>
<ul>
<li><a href="#orgheadline20">7.1. 表单的css样式控制</a></li>
</ul>
</li>
<li><a href="#orgheadline22">8. 参考资料</a></li>
</ul>
</div>
</nav>


# 前言<a id="orgheadline1"></a>

已经之前接触flask有一段时间了，所以关于web框架的一些基本概念这里都忽略了。

# 一些命令<a id="orgheadline8"></a>

## 新建一个项目<a id="orgheadline2"></a>

一开始需要新建一个项目

    django-admin startproject project-name

## 新建一个app<a id="orgheadline3"></a>

然后就是新建一个app

    python manage.py startapp app_name

## 数据库操作<a id="orgheadline4"></a>

定义模型之后，你需要运行:

    python manage.py makemigrations app_name

这个过程将自动根据你创建的模型在数据库中创建表格。然后后面你可能改动了模型的一些内容，则要执行:

    python manage.py migrate

也就是所谓的数据库迁移操作，不过大家都知道，数据库迁移并不是万能的，在某些情况下可能需要你手工单独处理的。

## 交互式环境<a id="orgheadline5"></a>

进入python交互环境，这个和纯python交互环境的区别就是里面可以直接使用django里面的一些东西了，比如你定义的模型对象就可以直接使用了。

    python manage.py shell

或者进入sql实现的交互环境:

    python manage.py dbshell

## 创建超级用户<a id="orgheadline6"></a>

    python manage.py createsuperuser

创建的超级用户可以用于登录和登录django的 admin 界面。

## 运行网络服务器<a id="orgheadline7"></a>

最后当然是最重要的主角了，开启网络服务器:

    python manage.py runserver

# url分发<a id="orgheadline10"></a>

web框架的一个核心功能就是完成url分发工作，我们先来看下django的这块内容。

基本过程是在你的 project 的 `urls.py` 那里定义好整个项目的url分发规则。默认的内容如下:

    from django.conf.urls import url
    from django.contrib import admin
    
    urlpatterns = [
        url(r'^admin/', admin.site.urls),
    ]

读者有兴趣可以先看下那个admin页，在 `/admin` 那边。请确认已经执行前面的数据库操作 `makemigrations` 和 `migrate` 了，然后已经创建超级用户了 `createsuperuser` 。这样你就可以看到django默认自动创建的admin支持页面了。

然后接下来就是类似这样的在这里插入你自己的 app的 各个 urls定义。一般如下简单写上即可:

    from django.conf.urls import include
        ....
        url(r'', include('app_name.urls')),
        ....

下面重点来了，具体在某个app下的 `urls.py` 就会定义各个具体的url分发规则。其语法如下所示:

    from django.conf.urls import url
    
    from . import views
    
    urlpatterns = [
        url(r'^$', views.index, name='index'),
    ]

views下面定义这具体的视图函数，这里最核心的内容就是: url这个函数和理解正则匹配规则。

1.  这个正则匹配规则和Tornado框架的url分发很类似，除了在风格上，Django这边开头都不写 `/` 然后带个开头 `^` 的正则符号。然后里面都很类似。比如url可以接受参数:

    from django.conf.urls import url
    
    from . import views
    
    urlpatterns = [
        url(r'^add/([\d]+)/([\d]+)$', views.add, name='add'),
    ]

这里参数将逐个传递个视图函数，唯一值得一提的是django的视图函数默认第一个函数是传递进去的 `request` 参量。在 `views.py` 里面的内容如下:

```python
from django.http import HttpResponse
# Create your views here.


def add(request, a, b):
    res = int(a) + int(b)
    return HttpResponse(str(res))
```

1.  `name` 这个参量大体类似于flask的 `endpoint` 的概念，然后django还有的 `reverse` 函数，其大体类似于flask的 `url_for` 的概念。

比如上面视图函数的 add 对应的url我们可以如下获得:

    from django.core.urlresolvers import reverse
    reverse('add',args=(1,2))

然后在模板中有:

    <a href="{% url 'add' 1 2 %}">link</a>

## 获取full-url<a id="orgheadline9"></a>

上面提到的reverse函数返回的url字符串还不是完整的url，如果我们要获取全站的完整url则可以使用 `request.build_absolute_uri(location)` ，如果不指定location则是当前的url。

# 模型<a id="orgheadline17"></a>

django的模板和sqlalchemy还是有很多地方类似的，不过还是有一些区别吧。在项目文件夹的 `settings.py` 文件里面现在有些配置需要了解了:

-   **INSTALLED\_APPS:** 你需要加上你新加入的 app 的名字，这个应该和django管理各个项目模型文件有关吧。
-   **DATABASES:** 默认会创建一个sqlite3数据库，也能满足基本的需求了，如果你想要使用mysql等数据库，则参考样例修改这里的配置。比如连接mysql的样例是:

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': os.environ.get("DB_NAME", "database_name"),
            'USER': os.environ.get("DB_USER", "root"),
            'PASSWORD': os.environ.get("DB_PW", ""),
            'HOST': os.environ.get("DB_HOST", "localhost"), 
            'PORT': os.environ.get("DB_PORT", "3306"),
        }
    }

## 默认创建的表格<a id="orgheadline11"></a>

什么模型定义都不写，django大概就会创建十个表格，简单了解一下这些表格还是很有用的。比如说:

-   **auth\_user:** 这是django用户登录管理的默认user表格，你会看到之前你创建的superuser信息就在这里。然后还有几个表格都和用户的群组权限管理相关。
-   **django\_migrations:** 存储着你的数据库迁移信息。
-   **django\_session:** 这个表格很有意思，如果你不登录你是看不到这个表格的，而且里面的内容是动态的。这就要讲到django的session机制不是基于传统的cookie，而是在这里数据库里面管理的。后面我们会继续深入了解django的session和middleware中间套件机制。

好了，开始实际定义自己的模型了。首先基本语法如下:

    from django.db import models
    
    
    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')

这个熟悉sqlalchemy的对这段代码不会很陌生，下面进一步了解一些细节吧。

## 字段类型<a id="orgheadline12"></a>

-   **IntegerField:** 整型
-   **BigIntegerField:** 大整数
-   **BinaryField:** raw data
-   **BooleanField:** bool 值

-   **CharField:** 定义字符串类型，常用的参数设置 `max_length` ，设置最大长度。对应的表单类型是 `TextInput` ，这个后面再说。
-   **TextField:** 大段文字用这个，对应的表单类型是 `Textarea`
-   **DateField:** 对应python中的 `datetime.date` 对象。
-   **TextField:** python的 `datetime.time` object
-   **DateTimeField:** 对应python中的 `datetime.datetime` 对象。

一个有用的基类:

```python
class BaseModel(models.Model):
    class Meta:
        abstract = True

    updated_datetime = models.DateTimeField(auto_now_=True)
    created_datetime = models.DateTimeField(auto_now_add=True)
```

后面的模型都可以继承自该基类，基类是不会创建表格的，通过 `class Meta` 然后 `abstract=True` 来获得。这里的 `auto_now` 设置为 `True` 意思是该字段每次 `save` 操作都会自动更新最新日期。 `auto_now_add` 设置为 `True` 即该对象第一次创建时设置最新的日期。

-   **DecimalField:** 对应python中的 Decimal object
-   **DurationField:** 对应python中的 timedelta object
-   **FilePathField:** 对应python的 float object

-   EmailField
-   FileField
-   FilePathField
-   ImageField
-   GenericIPAddressField
-   NullBooleanField

-   PositiveIntegerField
-   PositiveSmallIntegerField
-   SlugField
-   SmallIntegerField
-   URLField
-   UUIDField

## 通用可选项<a id="orgheadline13"></a>

字段声明控制中有一些通用可选项参量:

-   **default:** 设置该字段的默认值，注意default还可以接受一个函数对象。
-   **null:** 设置为True，则该自动会自动填充sql中的NULL值，字符串类型字段最好默认空字符。
-   **blank:** 如果设置为True，则空值也是允许的，否则需要一个明确的值。
-   **choices:** 该字段的可选值。

    from django.db import models
    
    class Student(models.Model):
        YEAR_IN_SCHOOL_CHOICES = (
            (FRESHMAN, 'Freshman'),
            (SOPHOMORE, 'Sophomore'),
            (JUNIOR, 'Junior'),
            (SENIOR, 'Senior'),
        )

-   **db\_column:** 设置该字段具体在数据库中的名字。

-   **db\_index:** 设置为 `True` 则表示该字段开启索引。

-   **primary\_key:** 主键，值得一提的是 `pk` 在django的models里面有特殊含义，其一定对应的就是该表格的主键，一般推荐使用 `pk` ，因为有时某些表格的主键名字不叫 `id` 。

-   **unique:** 唯一
-   **unique\_for\_date:** 比如title字段设置:

    unique_for_date="pub_date"

则 title字段和 pub\_date 字段都不能相同。也就是在某个日期内某个title只能有唯一值。可以看作一种 `unique_together` 的应用吧:

## 数据库中的关系<a id="orgheadline14"></a>

-   **ForeignKey:** 外键引用，如果该字段的名字是user，那么实际存储在表格中的名字是user\_id，然后你可以以ORM风格设置该字段如:

    user = User(...)

-   ManyToManyField
-   OneToOneField

## ORM层的使用方法<a id="orgheadline16"></a>

不废话，上代码，看一下就清楚了。

    from people.models import Person
    Person.objects.create(name="WeizhongTu", age=24)
    
    
    Person.objects.get(name="WeizhongTu")
    
    p.save()
    
    Person.objects.get_or_create(name="WZT", age=23) 
    # 返回一个元组，第一个为Person对象，第二个为True或False, 新建时返回的是True, 已经存在时返回False.
    
    Person.objects.all()
    
    Person.objects.filter(name="abc")
    # 等于Person.objects.filter(name__exact="abc") 名称严格等于 "abc" 的人
    
    Person.objects.filter(name__iexact="abc") 
    # 名称为 abc 但是不区分大小写，可以找到 ABC, Abc, aBC，这些都符合条件
    
    Person.objects.filter(name__regex="^abc") # 正则表达式查询
    
    Person.objects.filter(name__iregex="^abc")# 正则表达式不区分大小写
    
    Person.objects.filter(name__icontains="abc") #名称中包含 "abc"，且abc不区分大小写
    Person.objects.filter(name__contains="abc") # 名称中包含 "abc"的人
    
    Person.objects.exclude(name__contains="WZ") # 排除包含 WZ 的Person对象
    
    Person.objects.filter(name__contains="abc").exclude(age=23) 
    # 找出名称含有abc, 但是排除年龄是23岁
    
    Author.objects.all().order_by('name')
    Author.objects.all().order_by('-name') # 在 column name 前加一个负号，可以实现倒序
    
    Author.objects.filter(name__contains="WeizhongTu").filter(email="tuweizhong@163.com")
    Author.objects.filter(name__contains="Wei").exclude(email="tuweizhong@163.com")
    
    Author.objects.order_by('-id')[:20] # 反向索引

### 去重<a id="orgheadline15"></a>

    qs1 = Pathway.objects.filter(label__name='x')
    qs2 = Pathway.objects.filter(reaction__name='A + B >> C')
    qs3 = Pathway.objects.filter(inputer__name='WeizhongTu')
     
    # 合并到一起
    qs = qs1 | qs2 | qs3
    这个时候就有可能出现重复的
     
    # 去重方法
    qs = qs.distinct()

# 配置<a id="orgheadline18"></a>

获取django的配置:

    from django.conf import settings

配置里面有一些东西下面慢慢讲解之。

# 国际化<a id="orgheadline19"></a>

国际化或者说一些语言地区的配置。

    LANGUAGE_CODE = 'zh'
    
    TIME_ZONE = 'Asia/Shanghai'
    
    USE_I18N = True
    
    USE_L10N = True
    
    USE_TZ = True

POST 某个方法之后 redirect ，然后需要显示某个动作

# 表单<a id="orgheadline21"></a>

首先定义一个表单对象，如下面这种写法很是简洁，但不清楚和 `forms.Form` 那种写法比较起来有何局限。

    from django.forms import ModelForm
    from .models import YourModel
    
    
    class Game2048InfoForm(ModelForm):
    
        class Meta:
            model = YourModel
            fields = ['target_field_name'...]

在模板文件里面的使用如下:

    {{ form.name }}

对应的就是

    <input type="what" ...>

然后注意form下面要加上:

    {% csrf_token %}

其他就常规html编写风格吧。然后视图层那边，先看一下下面这个样例代码。

```python
def index(request):
    if request.method == 'POST':# 当提交表单时

        form = AddForm(request.POST) # form 包含提交的数据

        if form.is_valid():
            form.save()
        else:
            logging.warning('form invalid')
    else:# 当正常访问时
        form = AddForm()
    return render(request, 'index.html', {'form': form})
```

如果要提交文件的话，模板form那边记得加上:

    enctype="multipart/form-data"

然后form对象初始化如下所示:

    form = Game2048InfoForm(
        request.POST, request.FILES, instance=target_info)

上面例子还有个很有用的Tips，就是你运行下面的 `form.save()` 。如果你希望更好的对接数据库操作，那么就如上指定具体本form对象对应数据库中的那个model instance。这个在前面可以通过 `get_or_create` 来获得。这样就可以更精确地对应响应的模型的某条记录修改那些属性了。

## 表单的css样式控制<a id="orgheadline20"></a>

主要参考了 [这个网页](http://stackoverflow.com/questions/5827590/css-styling-in-django-forms) ，该网页介绍了几种风格吧，我更喜欢下面这种写法:

```python
class Game2048InfoForm(ModelForm):
    class Meta:
        model = Game2048Info
        fields = ['banner', 'banner_mobile', 'footer', 'footer_mobile',
                  'title', 'info', 'guize']

    def __init__(self, *args, **kwargs):
        super(Game2048InfoForm, self).__init__(*args, **kwargs)
        self.fields['title'].widget.attrs.update({'class' : 'form-control'})
        self.fields['info'].widget.attrs.update({'class' : 'form-control'})
        self.fields['guize'].widget.attrs.update({'class' : 'form-control'})
```

具体我们大体可以猜到每个field都有一个widget属性，其有attrs属性，这是一个字典值。

# 参考资料<a id="orgheadline22"></a>

1.  [django girls](http://tutorial.djangogirls.org/zh/installation/)
2.  [django基础教程](http://www.ziqiangxuetang.com/django/django-tutorial.html)