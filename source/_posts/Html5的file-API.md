---
title: Html5的file API
date: 2016-10-21 23:54:13
tags: html5
---
> 在*html4*的年代，我们如果要在网页上呈现一张用户本地的图片，需要用户先把图片上传到服务器，再根据服务器提供的图片地址把图片下载下来，才能把图片在网页上呈现出来。这一来二往，起码已经费了两倍于这张图片的流量了，更别说服务器为了存储这张图片所花费的资源以及用户上传错了图片的冤枉成本（因为在html4时代，用户选择好图片后，往往只能看到图片的文件名，而无法通过预览图来进一步确认这张图片是否就是自己想要上传的）。html5提供了新玩法，光靠浏览器，就可以对本地的（其实也可以是远程的）文件（主要是图片）进行**呈现、读取、处理**等操作，而这一切，则是通过html5 **file api**来实现的。

  首先是*数据结构*，html5定义了一个**file**对象类型来表示文件，每个file对象对应一个文件。file对象有3个属性：*name*、*size*、*type*。name是不包含路径的文件名，size是以字节为单位的文件体积大小，type则是文件的MIME（例如image/jpg）。

  file对象不单独存在，而是以数组形式，存在一个名为FileList的数组中。那么，如何拿到这个FileList数组呢？目前，html5有两个途径可以拿到FileList，**一是通过file类型的input**，**二是通过拖放操作的drop事件**。

### 通过file类型的input获取FileList
``<input id="file-input" type="file" />``
或html5新增的可进行文件多选的multiple属性：

``<input id="file-input" type="file" multiple />``
一般，我们都是给input:file绑上一个*onchange*事件，以便在用户选定文件后，马上进行读取文件等下一步操作：
{% codeblock lang:js %}
//原生js
var inputElement = document.getElementById("file-input");
inputElement.addEventListener("change", handleFiles, false);
function handleFiles() {
  var fileList = this.files; 
}

//jquery版
$('#file-input').on('change', function() {
    var fileList = this.files;
});
{% endcodeblock %}
### 通过拖放操作drop事件
首先要设定一个可供拖放的区域：
``<div id="dropbox" style="width: 200px;height: 200px;"></div>``
另外，为了能**触发drop**事件，我们必须阻止dragenter和dragover事件的默认行为：
{% codeblock lang:js %}
var dropbox;
dropbox = document.getElementById("dropbox");
dropbox.addEventListener("dragenter", dragenter, false);
dropbox.addEventListener("dragover", dragover, false);
dropbox.addEventListener("drop", drop, false);
function dragenter(e) {
  e.stopPropagation();
  e.preventDefault();
}

function dragover(e) {
  e.stopPropagation();
  e.preventDefault();
}
{% endcodeblock %}
然后，我们就可以在drop事件的callback中，获取到fileList：
{% codeblock lang:js %}
function drop(e) {
  e.stopPropagation();
  e.preventDefault();

  var dt = e.dataTransfer;
  var files = dt.files;

  handleFiles(files);
}
{% endcodeblock %}
<!--more-->
>  怎么读取或利用file对象呢？html5提供了两个方案：**FileReader**和**ObjectUrl**。

### 使用FileReader读取file对象
首先需要实例化FileReader对象：
``
var reader = new FileReader();
``
利用FileReader读取file对象是一个**异步**的过程，我们需要先为FileReader设置好load事件的callback，告知FileReader在读取到file对象的数据后应该进行什么进一步的操作：
``
reader.onload = function(e) { 
    document.getElementById("image").src = e.target.result; 
}
``
上面这段代码的意思是，FileReader读取到图片的数据后，把数据（DataUrl）放到<img>的src属性里。最后，就是通过FileReader不同的方法，来决定读取file对象数据后用什么数据格式来存放，并实施读取：

1. readAsArrayBuffer(file) ：读取file对象并存放为ArrayBuffer对象（ArrayBuffer对象是什么我暂时没有搞清，应该是一种为了高效存取数据而产生的数据结构）。
2. readAsText(file [, 'UTF-8']) ：以一般文本模式读取file对象，值得注意的是，利用第二个参数（可选）可指定字符编码。
3. readAsDataURL(file) ：读取file对象并存放为data: URL格式的字符串。

### 利用ObjectURL
ObjectURL相当于文件的一个临时路径，此临时路径可随时生成、随时释放，在本地浏览器使用起来时，与普通的url无异。以把一张本地图片显示在页面上为例：
``
var img = document.createElement("img");
img.src = window.URL.createObjectURL(file);
``
此时，src形如：blob:http://test.local.com/e03e8bbf-66ce-4fea-a8c8-772f9fdb4d40
用这个src就能让浏览器从本地读取图片。
这种方案相对用FileReader生成图片的base64编码并放到<img>的src里来说，性能有了很大的提升。

比较这两种读取File对象的方案，**FileReader适合用来上传文件**，而**ObjectURL则适合直接在浏览器进行操作，然后操作后再把处理后的数据进行上传，例如利用canvas截图或进行图片压缩等**。当然，这一切都是要考虑*兼容性*的。