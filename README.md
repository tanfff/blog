
#兼容
- 1.attachEvent  添加事件，示例如下
- 2.detachEvent  删除事件，示例如下
```
<input id="btnClick" type="button" value="Click Here" />
```
```
<script type="text/javascript">
    var btnClick = document.getElementById('btnClick');
    var handler=function() {
        alert(this.id); //ps:this指的是windows
    }
    btnClick.attachEvent('onclick', handler);//添加事件
    btnClick.detachEvent('onclick', handler);//删除事件
</script>
```
###** 两者的区别 **

1.参数个数不相同，这个最直观，addEventListener有三个参数，attachEvent只有两个，attachEvent添加的事件处理程序只能发生在冒泡阶段，addEventListener第三个参数可以决定添加的事件处理程序是在捕获阶段还是冒泡阶段处理（我们一般为了浏览器兼容性都设置为冒泡阶段）

2.第一个参数意义不同，addEventListener第一个参数是事件类型（比如click，load），而attachEvent第一个参数指明的是事件处理函数名称（onclick，onload）

3.事件处理程序的作用域不相同，addEventListener的作用域是元素本身，this是指的触发元素，而attachEvent事件处理程序会在全局变量内运行，this是window，所以刚才例子如果没有删除事件，只有添加事件执行会返回undefined，而不是元素id

4.为一个事件添加多个事件处理程序时，执行顺序不同，addEventListener添加会按照添加顺序执行，而attachEvent添加多个事件处理程序时顺序无规律(添加的方法少的时候大多是按添加顺序的反顺序执行的，但是添加的多了就无规律了)，所以添加多个的时候，不依赖执行顺序的还好，若是依赖于函数执行顺序，最好自己处理，不要指望浏览器

  //根据上述几点写出的兼容写法
   ```
        function addEvent(node, type, handler) {
            if (!node) return false;
            if (node.addEventListener) {
                node.addEventListener(type, handler, false);
                return true;
            }
            else if (node.attachEvent) {
                node.attachEvent('on' + type, handler, );
                return true;
            }
            return false;
        }
	```
	
	
###上面讲到了this指向不同，下面就解决一下this的问题

###添加事件处理程序

```
   function addEvent(node, type, handler) {
        if (!node) return false;
        if (node.addEventListener) {
            node.addEventListener(type, handler, false);
            return true;
        }
        else if (node.attachEvent) { //此函数这里看着很复杂，我们来做个简化，令'e' + type + handler为k
            node['e' + type + handler] = handler;  //给node的k属性赋值，值为handler函数，注意在此函数中的this是指node节点，化解了this的问题
            node[type + handler] = function() {
            node['e' + type + handler](window.event);  // 调用node结点的k属性，并传入参数windows.event,之所以增加这一步，就是为了传入此参数
            };
            node.attachEvent('on' + type, node[type + handler]); //绑定attachEvent事件处理程序 ，事件发生即可调用node结点的k属性
            return true;
        }
        return false;
    }
//取消事件处理程序
```

```
function removeEvent(node, type, handler) {
    if (!node) return false;
    if (node.removeEventListener) {
        node.removeEventListener(type, handler, false);
        return true;
    }
    else if (node.detachEvent) {
        node.detachEvent('on' + type, node[type + handler]);           node[type + handler] = null;                           
    }
    return false;
}
  //上述步骤的属性k之所以要用'e' + type + handler，就是为了再取消事件处理程序时找到对应的属性，简而言之，就是一个标识
```


###事件对象的不同属性方法

###DOM中的事件对象和IE中的事件对象有不同的属性/方法



```
>function getEvent(e) {
    return e || window.event;
} // 获取事件



>function getTarget(e) {
    return e.target || e.scrElement;
} //获取事件的目标元素



function preventDefault(e) {
    if (e.preventDefault)
        e.preventDefault();
    else
        e.returnValue = false;
}  //取消默认事件



function stopPropagation(e) {
    if (e.stopPropagation)
        e.stopPropagation();
    else
        e.cancelBubble = true;
}  //阻止冒泡
```
