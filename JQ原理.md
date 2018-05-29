##JQ的核心原理

> ###JQ的版本和下载
> 常用版本 ：jquery-1.9.3.min.js  jquery-1.11.3  jquery.min.js
> 推荐书籍：锋利的JQ第二版
> 
> JQ的核心原理解读
> JQ是一个常用的方法类库（常用的DOM类库），提供了很多真实项目开发中需要使用的属性和方法（这些方法JQ已经帮我们完善了浏览器兼容处理以及一些细节的优化）
> 
> `jQuery本身是一个类（JQ是基于构造函数模式构建的类库）

```javascript
var jQuery = function (selector,context){
    return new jQuery.fn.init(selector,context);
}

jQuery.fn = jQuery.prototype = {
    jquery : version,
    constructor : jQuery
}
...
init = jQuery.init = function (selector,context){
}
```
