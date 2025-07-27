title: 【算法和数据结构】—列表     
date: 2020-03-16
tags: [JavaScript、算法]
categories: [总结]
---

"列表"是个相对简单的结构，说简单，不是形式，而是操作复杂度，因为从形式上看，它和“栈、队列”，是没有什么差别的，区别在于计算的方式，本文主要研究“列表”。

## 抽象数据

想用程序解决问题，就需要把问题转换成程序语言，所以第一步是“抽象”。

想想看，“列表”有哪些属性？

- 数据项：元素
- 元素个数：长度
- 查找元素
- 插入：表头、表尾，或者任意两个元素中间
- 删除或者清空操作
- 显示所有元素
- 获取当前元素
- 元素位置
- 移动查找

## 定义

由上面整理的数据，我们可以把“列表”这个类给封装定义出来。

```js
    function List(){
      this.listSize = 0;
      this.pos = 0;
      this.dataStore = 0;
      this.clear = clear;
      this.find = find;
      this.toString = toString;
      this.insert = insert;
      this.append = append;
      this.remove = remove;
      this.prev = prev;
      this.next = next;
      this.moveTo = moveTo;
    }
```

## 实现

### 添加（append）

此处只展示在末尾添加，你当然可以在任意位置进行添加。

```js
    function append(ele){
      this.dataStore[this.listSize++] = ele;
    }
```

### 删除（remove）

删除需要分成几个步骤：查找、删掉、填补。

先来实现“查找（find）”。

```js
    function find(ele){
      for(var i;i<this.listSize;i++){
        if(dataStore[i] == ele){
          return i;
        }
      }
      return -1;
    }
```

而删除方法就需要从`find()`方法返回的位置对列表进行截取，改变后，将列表长度减1，如果删除成功返回true，否则返回false。

```js
    function remove(ele){
      var fundAt = this.find(ele);
      if(fundAt > -1){
        this.dataStore.splice(fundAt,1);
        --this.listSize;
        return true;
      }
      return false;
    }
```

`length`和`toString()`较为简单，直接返回相应的值就好，此处就不写了。

写到这儿，不妨先检测一下效果如何，新建一个列表的实例。

```js
    var nameList = new List();
    nameList.append("新生1");
    nameList.append("新生2");
    nameList.append("新生3"); // ["新生1", "新生2", "新生3"]
    nameList.remove("新生2"); // ["新生1", "新生3"]
```

代码顺利执行得到正确的结果，接着往下看。

### 插入（insert）

既然是插入，那就是一个相对位置，找到目标位置，将新元素放进去，听起来跟上面的`remove()`方法有相似之处，实际也是一个原理。

```js
    function insert(ele,after){
      var insertPos = this.find(after);
      if(insertPos > -1){
        this.dataStore.splice(insertPos+1,0,ele);  // 此处有个坑，记得位置数字加1，否则就插到前面了
        ++listSize;
        return true;
      }
      return false;
    }
```

还拿上面的示例来看，我们将删除的“新生2”的位置用一个新元素“神秘来宾”补充。

```js
    nameList.insert("神秘来宾","新生1"); // ["新生1", "神秘来宾", "新生3"]
```

### 清空（clear）

清空操作就有点暴力了，有一定危险性，不过也在常规操作的范畴。

```js
    function clear(){
      delete this.dataStore;
      this.dataStore = [];
      this.listSize = this.pos = 0;
    }
```

但还是要注意，这里是清空，而不是删除，列表还在，只是内容为空，所以，把旧列表删了又新建了个空的列表。

### 移动查找（next、prev、moveTo）

列表项的移动分三种：前、后、指定位置。

```js
    function prev(){
      if(this.pos>0){
        --this.pos;
      }
    }

    function next(){
      if(this.pos<this.listSize-1){
        ++this.pos;
      }
    }

    function moveTo(position){
      this.pos = position;
    }
```

这些方法起来很简单，但也是有对应的场景，譬如：焦点图的上一张、下一张、跳到指定张等。

## 应用

说了这么多，“列表”这种结构有哪些用途呢？很常见，比如：待办事项、购物清单、排行榜等等，具体代码就不再这里罗列，只不过是代码的具体场景化而已，这种结构相对很好理解，就先聊到这儿，我们下篇见。






