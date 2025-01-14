<nav id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgheadline3">1. 均值 中位数 众数</a>
<ul>
<li><a href="#orgheadline1">1.1. 用python来计算</a></li>
<li><a href="#orgheadline2">1.2. 意义</a></li>
</ul>
</li>
<li><a href="#orgheadline6">2. 极差 中程数</a>
<ul>
<li><a href="#orgheadline4">2.1. 用python来计算</a></li>
<li><a href="#orgheadline5">2.2. 意义</a></li>
</ul>
</li>
<li><a href="#orgheadline8">3. 绘制棒状图，线性图，饼状图</a>
<ul>
<li><a href="#orgheadline7">3.1. 何时用什么图</a></li>
</ul>
</li>
<li><a href="#orgheadline9">4. 样本和总体</a></li>
<li><a href="#orgheadline11">5. 方差和标准差</a>
<ul>
<li><a href="#orgheadline10">5.1. 意义</a></li>
</ul>
</li>
</ul>
</div>
</nav>


# 均值 中位数 众数<a id="orgheadline3"></a>

请参看可汗学院的视频，网易公开课对应视频在[这里](http://v.163.com/movie/2011/6/6/0/M82IC6GQU_M83J9IK60.html) 。


<embed src="http://swf.ws.126.net/openplayer/v02/-0-2_M82IC6GQU_M83J9IK60-vimg1_ws_126_net//image/snapshot_movie/2014/4/O/U/M9OD1F0OU-1430711943278.swf" width="640" height="360" allowFullScreen="true" wmode="transparent" style="margin-left: auto;margin-right: auto; display: block;"/>

mean，也就是均值，也就是算术平均数，也就是大家熟知的把所有的数加起来然后除以个数，看看这些数平均有多大。

median,中位数，是这样的概念：如果我们把一组数从小到大排列，然后最中间的那个数就是中位数。其中最中间的解释是，如果一共有奇数个数字，那么去掉一个最小去掉一个最大，这样类推，最后剩下来的那个数就是中位数；如果一共有偶数个数字，那么最后会剩下两个数字，而中位数就是最后这两个数字的均值。

mode，众数，也就是这一组数中出现频率最高或者说分布最多的数。比如说[1,2,2,3]这样一个样本，其中数字2出现次数最多，我们就说这个样本的众数为2。

## 用python来计算<a id="orgheadline1"></a>

自python3.4起，python加入了statistics模块了，所以我们不需要重复发明轮子了。上面的mean，median和mode这三个名字在statistics模块中都是对应的函数，下面是一些演示例子：

    from statistics import mean,mode,median
    
    >>> lst = [23,29,20,32,23,21,33,25]
    >>> mean(lst)
    25.75
    >>> median(lst)
    24.0
    >>> mode(lst)
    23

这三个函数都接受一个可迭代对象，然后进行相关操作，如果是空数据，那么将raise **statistics.StatisticsError** 。

值得注意的是mode还可以操作字符串：

    >>> mode(['a','b','a','c','d'])
    'a'

然后mode函数并不能处理几个数都是频率最高的情况，比如运行下面命令，将raise `statistics.StatisticsError` 。

    >>> lst = [1,1,2,3,3]
    >>> mode(lst)

我们可以利用collections模块的Counter类的most\_common方法来获得更健壮的mode函数，此时不可避免的mode函数要返回的是一个列表值了。

```python
from collections import Counter
import statistics

def mode(lst):
    c = Counter(lst)
    result = [c.most_common()[0][0]]
    for i in range(1,len(c)):
        if c.most_common()[i][1] == c.most_common()[0][1]:
            result += [c.most_common()[i][0]]
        else:
            break
    return result
```

## 意义<a id="orgheadline2"></a>

平均值的概念是大家最熟悉的，其是最基本的统计学手段。比如物理学上的测量，为了精确总是需要多次测量取平均值。再比如我们衡量某一类数据的大致平均情况，比如你的考试平均成绩，通过这个平均成绩可以大致得到你对待本学期学业的态度等。

中位数和均值一样也能考察样本总体存在状态，不过区别是中位数对一些极值（特殊的偏离特别远的值）不敏感。以收入来举例子：某些人的收入特别的高，那么如果你用均值来考察社会的收入水平就会有抬高的嫌疑。因为不同的家庭之间的收入并没有共享，但是你在计算中却一并考虑了。这个时候用中位数来考察能更加合理地看着社会总体的收入水平。因此考察家庭平均收入要用中位数。

众数只关注出现次数最多的数，严格来讲其并不具有考察样本总体存在状态的能力了，不顾在某些情况下却可以。比如说你头发的颜色这样的定性描述，用众数就很合适。

# 极差 中程数<a id="orgheadline6"></a>

请参看可汗学院的视频，网易公开课对应视频在 [这里](http://v.163.com/movie/2011/6/D/S/M82IC6GQU_M83J9J8DS.html) 。

<embed src="http://swf.ws.126.net/openplayer/v02/-0-2_M82IC6GQU_M83J9J8DS-vimg1_ws_126_net//image/snapshot_movie/2014/4/C/L/M9OD1GBCL-1430711943278.swf"  width="640" height="360" allowFullScreen="true" wmode="transparent"  style="margin-left: auto;margin-right: auto; display: block;"/></embed>

极差就是这一组数的最大值和最小值的差值。中程数就是这一组数的最大值和最小值的均值。

## 用python来计算<a id="orgheadline4"></a>

因为极差range这个名字和python语言的range函数相冲突，所以python并没有为极差定义一个函数，由于计算公式较简单，我们也不需要额外定义一个公式。就是：

```python
max(lst) - min(lst)
```

而中程数就是

```python
(max(lst) + min(lst))/2
```

## 意义<a id="orgheadline5"></a>

极差是统计学上入门级别的粗略的对样本的离散程度的考察，比如一组数不是很分散，都集中在均值附近，那么其极差就会很小，而同样的情况另一组数如果极差更大，那么我们说第二组数离散程度更大。

中程数可以作为如果样本数组离散程度不是很高的话，作为均值的近似快速计算。

# 绘制棒状图，线性图，饼状图<a id="orgheadline8"></a>

请参看 matplotlib模块 这篇文章。

## 何时用什么图<a id="orgheadline7"></a>

棒状图一般用来表现分类，分布；线性图一般用于表现变化趋势；饼状图一般用来表现百分比的占有率。其中棒状图如果样本数目特别大，会考虑用点状图来表示，因为棒柱子画得太多了很影响图形的美观，点状如果过于密集，也会很不美观，会考虑使用线性图。可以近似看作点状图是棒状图和线性图的中间过渡态。

一般作图都尽量用这三个图，还有其他图，比如说箱线图，确实，箱线图是很适合描述数据的离散情况，但毕竟作图就是为了大多数人的直观感觉，像箱线图这种一般人不是很熟悉的图形（可能就生物专业的接触得比较多）还是少用比较好。描述分布前面说了用棒状图或者点状图（更适合空间的情况）来描述也很合适。至于说到中位数等等统计学计算出来的数值，在图形细节说明上补充即可。

其实箱线图概念还是很简单的，不熟悉的可以参看可汗学院的相关两个视频，网易公开课对应链接在这里：[箱线图1](http://v.163.com/movie/2011/6/H/I/M82IC6GQU_M83JA1FHI.html) [箱线图2](http://v.163.com/movie/2011/6/E/2/M82IC6GQU_M83J9UME2.html) 。

# 样本和总体<a id="orgheadline9"></a>

简单来说，总体就是整个你要考察的对象，即使是对于上面简单的均值，中位数，众数等等描述性概念，对于总体来说通常都是很难全部考察的。于是我们从总体中取出某一些考察对象（理论上要求是近似于完全的随机取样），然后组成一个样本。然后我们希望对于样本的一些统计描述性数据能够很好地反应总体的数据存在状况。这样所有统计学量实际上都有两种类型，比如均值有总体均值 \(\mu\) 和样本均值 \(\overline{X}\) 。

# 方差和标准差<a id="orgheadline11"></a>

首先来说总体的情况，总体均值是 \(\mu\) ，而总体方差具体公式如下所示:

\begin{equation}
{\sigma  }^{2  } =\frac { 1 }{n }  \sum _{ i=1 }^{ n }{( X_i  - \mu)^2}
\end{equation}

也就是总体方差是每一个数和总体均值的差的平方和的均值。

    >>> from statistics import *
    >>> pvariance([0,0,5,5])
    6.25

    statistics.pvariance(data, mu=None)

如上所示，pvariance还可以接受 `mu` 可选参数，也就是总体均值，这样可以避免重复计算，若没有赋值则函数会自动计算。

然后总体标准差就是总体方差的平方根，或者说就是上面的 \(\sigma\) 。

    >>> from statistics import *
    >>> pstdev([0,0,5,5])
    2.5

pstdev（Population standard deviation）同样还可以接受 `mu` 可选参数。

在实际应用中，绝大多数情况都是考察的样本而不是总体，所以更常用的是样本方差:

\begin{equation}
{S  }^{2  } =\frac { 1 }{n-1 }  \sum _{ i=1 }^{ n }{( X_i  - \overline { X } )^2}
\end{equation}

你可能已经注意到了，上面的是 \(n-1\) 而不是 \(n\) ，这其中有些统计学的考虑，什么无偏估计，也就是用样本来评估总体的方差的时候，总会估计值偏小，然后再加上一个额外的数据量来让估计值更准确些，大体就是这样，至于具体为何是 `n-1` ，那就是统计学里面的高级内容了，总之就是为了更好的估计（从样本估计总体）。

python中的样本方差是 `variance` 函数，样本标准差是 `stdev` 函数:

    >>> variance([0,0,5,5])
    8.333333333333334
    >>> stdev([0,0,5,5])
    2.886751345948129
    >>>

然后其提供的样本均值可选参数是 `xbar` :

    statistics.variance(data, xbar=None)

## 意义<a id="orgheadline10"></a>

方差是用来描述待考察的统计数据的离散程度的。

请参看可汗学院的 [第13课](http://open.163.com/movie/2011/6/V/8/M82IC6GQU_M83JA9NV8.html)  [第14课](http://open.163.com/movie/2011/6/6/N/M82IC6GQU_M83JA826N.html)  [第15课](http://open.163.com/movie/2011/6/P/P/M82IC6GQU_M83JACTPP.html) 。