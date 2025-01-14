<nav id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgheadline3">1. 安装和简介</a>
<ul>
<li><a href="#orgheadline1">1.1. 预安装</a></li>
<li><a href="#orgheadline2">1.2. 下载源码和安装</a></li>
</ul>
</li>
<li><a href="#orgheadline4">2. 入门</a></li>
<li><a href="#orgheadline5">3. 参考资料</a></li>
</ul>
</div>
</nav>


# 安装和简介<a id="orgheadline3"></a>

对于图像处理的一般处理需求，应该优先考虑用pillow模块。opencv模块进入之后就要考虑将图像理解为像素点矩阵，然后进行各种算法和数学运算了。要真正做到深入和灵活运用opencv模块，相关图像处理领域的很多理论知识是必须具备的了。这对于某些python用户可能过了些了。

## 预安装<a id="orgheadline1"></a>

官网教程也提到了需要预安装的一些软件包：

    [compiler] sudo apt-get install build-essential
    [required] sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev 
        libavformat-dev libswscale-dev
    [optional] sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev 
        libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev

不过官网说这只在Ubuntu 10.04 中测试过，你还可以参考[这篇文章](http://rodrigoberriel.com/2014/10/installing-opencv-3-0-0-on-ubuntu-14-04/) ，其是在Ubuntu 14.04 下做的测试：

    sudo apt-get -y install libopencv-dev build-essential cmake git 
        libgtk2.0-dev pkg-config python-dev python-numpy libdc1394-22 
        libdc1394-22-dev libjpeg-dev libpng12-dev libtiff4-dev libjasper-dev
        libavcodec-dev libavformat-dev libswscale-dev libxine-dev
        libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev libv4l-dev 
        libtbb-dev libqt4-dev libfaac-dev libmp3lame-dev libopencore-amrnb-dev 
        libopencore-amrwb-dev libtheora-dev libvorbis-dev libxvidcore-dev x264 
        v4l-utils unzip

## 下载源码和安装<a id="orgheadline2"></a>

opencv的github源码地址在 [这里](https://github.com/Itseez/opencv) ，推荐下载稍微前面点的版本会稳定，因为opencv还在活跃开发中。

在切换到opencv文件之后，新建了一个build文件夹，然后调用cmake命令，我们可以看到cmake最后对应的文件夹 “..“ 正是要去找寻那个文件：CMakeLists.txt。

    cd opencv
    mkdir build
    cd build
    cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local 
        -D BUILD_NEW_PYTHON_SUPPORT=ON  -D WITH_IPP=OFF  -D WITH_OPENGL=ON ..

不过这里cmake参数挺复杂的，其中

    -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local

都会这么写，然后

    -D BUILD_NEW_PYTHON_SUPPORT=ON

很重要，也就是安装了对应的python模块。

然后我在安装的时候出现了一个错误，参考 [这个网页](http://answers.opencv.org/question/37115/opencv-249-make-error/) 加上如下参数即可解决：

    -D WITH_IPP=OFF

然后rodrigoberriel<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup> 提及如果如果要使用Qt5，则不能加上 `-D WITH_QT=ON` ，请参看 [这篇文章](http://rodrigoberriel.com/2014/11/using-opencv-3-qt-creator-3-2-qt-5-3/) 。其他还有一些参数选项我暂时也不清楚有什么用，应该都和其他额外的功能有关，暂时都可加可不加吧。

然后常规的运行make 和 make install 来进行安装了，下面的 `$(nproc)` 对应的你机器的cpu数目。

    make -j $(nproc)
    sudo make install

和官网不同，rodrigoberriel还提出需要加上如下命令来完成安装：

    sudo /bin/bash -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'
    sudo ldconfig

安装完了你可以简单来查看一下：

    >>> import cv2
    >>> cv2.__version__
    '3.0.0-beta'

# 入门<a id="orgheadline4"></a>

裁剪

    import cv2
    img = cv2.imread("test.jpg")
    crop_img = img[200:400, 100:300] # Crop from x, y, w, h -> 100, 200, 300, 400
    # NOTE: its img[y: y + h, x: x + w] and *not* img[x: x + w, y: y + h]
    cv2.imshow("cropped", crop_img)
    cv2.waitKey(0)

# 参考资料<a id="orgheadline5"></a>

1.  OpenCV Computer Vision with Python , Author: Joseph Howse, First published: April 2013
2.  OpenCV-Python 中文教程 , 段立辉译
3.  Programming Computer Vision with Python, Author:Jan Erik Solem , First Edition: June 2012

<div id="footnotes">
<h2 class="footnotes">Footnotes: </h2>
<div id="text-footnotes">

<div class="footdef"><sup><a id="fn.1" class="footnum" href="#fnr.1">1</a></sup> <div class="footpara">[rodrigoberriel.com](http://rodrigoberriel.com/2014/10/installing-opencv-3-0-0-on-ubuntu-14-04/)</div></div>


</div>
</div>