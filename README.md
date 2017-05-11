# blog
兼容
- 1.attachEvent  添加事件，示例如下
- 2.detachEvent  删除事件，示例如下
><input id="btnClick" type="button" value="Click Here" />

><script type="text/javascript">
    var btnClick = document.getElementById('btnClick');
    var handler=function() {
        alert(this.id); //ps:this指的是windows
    }
    btnClick.attachEvent('onclick', handler);//添加事件
    btnClick.detachEvent('onclick', handler);//删除事件
</script>
