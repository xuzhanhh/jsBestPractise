## 最小化现场更新
一旦你需要访问的DOM部分是已经显示的页面的一部分，那么就是在进行一个**现场更新**。每一个更改，无论是插入单个字符，还是移除整个片段，都有一个性能惩罚，**因为浏览器要重新计算无数尺寸以进行更新**。现场更新执行得越多，代码完成执行所花得时间就越长。
```javascript
let list = document.getElementById("myList"),item,i;
for (i=0,i<10;1++){
    item = document.createElement("li");
	list.appendChild(item);
	item.appendChild(document.createTextNode("Item"+i));
}
```
一共20个现场更新。要减少现场更新的数量，一般有两种方法。
  1. 讲列表从页面上移除，最后进行更新，最后将列表插回同样的位置。
  2. 使用文档片段来构建DOM结构，接着将其添加到List元素中。
```
let list = document.getElementById("myList"),
    fragment = document.createDocumentFragment(),
    item,i;
for(i=0;i<10;i++){
	item=document.createElement("li");
	item.appendChild(document.createTextNode("item"+i));
	fragment.appendChild(item);
}
list.appendChild(fragment);
```

一旦需要更新DOM，请考虑使用文档片段来构建DOM结构，然后再将其添加到现存的DOM中。

## 使用innerHTML
有两种在页面上创建DOM节点的方法：使用诸如createElement()和appendChild()之类的DOM方法，以及使用innerHTML。对于小的DOM更改而言，两种方法效率都差不多。然而，对于大的DOM更改，使用innerHTML要比使用标准DOM方法创建同样的DOM结构快得多。

当把innerHTML设置为某个值时，后台会创建一个HTML解析器，然后使用内部的DOM调用来创建DOM结构，而非基于JavaScript的DOM调用。由于内部方法是编译好的而非解析执行，所以执行快得多。前面的例子还可以用innerHTML改写：
```
for(i=0;i<10;1++){
html+=`<li>item ${i}</li>`;
}
list.innerHtml = html;
```

## 使用事件代理
 大多数的Web应用在用户交互上大量用到事件处理程序。页面上的事件处理程序的数量也页面响应用户交互的速度之间是负相关。为了减轻这种惩罚，最好使用事件代理

## 注意HTMLCollection
任何时候HTMLCollection，不管是一个属性还是一个方法，都是在文档上进行查询，这个查询开销很昂贵。最小化访问HTMLCollection的次数可以及大地改进脚本的性能。
```
let images=document.getElementsByTagName("img"),
image,i,len;
for(i=0,len=iamges.length;i<len;i++){
image=images[i];
//do something...
}
```
编写JavaScript的时候，一定要知道何时返回HTMLCollection对象
1. getElementsByTagName()
2. 获取元素的childNodes属性
3. 获取元素的attributes属性
4. 访问了特殊的集合，如document.forms 、document.images等