##AJAX基础知识及核心原理解读

###AJAX基础知识

> 什么是ajax
> async javascript and xml，异步的JS和XML      

`XML：可扩展的标记语言` 

> 作用是用来存储数据的（通过自己扩展的标记名称清晰的展示出数据结构）
> ajax成为异步的js和xml，主要原因是：当初最开始用ajax实现客户端和服务端数据通讯的时候，传输的数据格式一般都是xml格式的数据，我们把称之为异步js和xml（现在一般都是基于JSON格式来进行数据传输的）

`异步的JS`

> 这里的异步不是说ajax只能基于异步进行请求（虽然建议都是使用异步编程），这里的异步特指的是**`局部刷新`**

`局部刷新 VS 全局刷新`

> 在非完全前后端分离项目中，前端开发只需要完成页面的制作，并且把一些基础的人机交互效果使用JS完成即可，页面中需要动态呈现内容部分，都是交给后台开发工程师做数据绑定和基于服务器进行渲染的（服务器端渲染）
> [优势]
> 1. 动态展示的数据在页面的源代码中可以看见，有利于SEO推广（有利于搜索引擎的收录和抓取）
> 2. 从服务器端获取的结果就已经是最后要呈现的结果，不需要客户端做额外的事情，所以页面加载速度快（前提是服务器端处理的速度够快，能够处理过来），所以类似于京东、淘宝这些网站，首屏数据一般都是经由服务器端渲染的
> [弊端]
> 1. 如果页面中存在需要实时需要更新的数据，每一次想要展示最新的数据，页面都要重新的刷新一次，这样肯定不行
> 2. 都交给服务器端做数据渲染，服务器端压力太大，如果服务器处理不过来，页面呈现的速度更慢（京东淘宝这类i网站，除了首屏是服务端渲染的，其他屏一般都是客户端做数据渲染绑定）
> 3. 这种模式不利于开发（开发效率低）
> 
> 目前市场上大部分项目都是前后端完全分离的项目（也有前后端完全分离的项目）
> 
> 前后端完全分离的项目吗，页面中需要动态绑定的数据交给客户端完成渲染的
> 1. 向服务端发送AJAX请求
> 2. 把从服务器端获取的数据解析处理，拼接成为我们需要展示的HTML字符串
> 3. 把拼接好的字符串替换页面中某一部分的内容（局部刷新），页面整体不需要重新加载，局部渲染即可
[优势]
> 1. 我们可以根据需求任意改变页面中的某一部分内容改变（例如实时改变），整体页面不刷新，性能好，体验好（所有表单验证，需要实时刷新的等需求都要基于AJAX实现）
2. 有利于开发、提高开发的效率
1）前后端的完全分离，后台不需要考虑前端如何实现，前端也不需要考虑后台用什么技术，真正意义上实现了技术的划分
2）可以同时进行开发：项目开发开始，首先制定前后端数据交互的接口文档（文档中包含了，调取哪个接口或者哪些数据等协议规范），后台把接口先写好（目前很多公司也需要前端自己拿NODE来模拟这些接口），客户端按照接口调取即可，后台再次去实现接口功能即可
> [弊端]
> 1. 不利于SEO优化：第一次从服务器端获取的内容各种不包含需要动态绑定的数据，所以页面的源代码中没有这些内容，不利于SEO收录，后期通过JS添加到页面中的内容，并不会写在页面的源代码中（是源代码不是页面结构）
> 2. 交由客户端渲染，首先需要把页面呈现，然后再通过JS的异步AJX请求获取数据，浏览器会把动态增加的部分重新渲染，无形中浪费了一些事件，没有服务器端渲染页面呈现速度快

###基于原生JS实现AJAX
```javascript
//=>创建一个AJAX对象
let xhr = new XMLHttpRequest();//=>不兼容IE6

//=>打开请求地址（可以理解为一些基础配置，但是并没有发送请求）
xhr.open([method],[url],[async],[username],[user password]);

//=> 监听AJAX状态改变，获取响应信息（获取响应头信息，获取响应主体信息）
xhr.onreadystatechage=()=>{
    if(xhr.readyState === 4 && xhr.status === 200){
        let result = xhr.responseTesxt; //=> 获取响应主体内容
    }
}

//=> 发送ajax请求(括号中传的就是请求主体的内容)
xhr.send(null);
```
`分析第二布中的细节点`

> xhr.open([method],[url],[async],[username],[user password]);

> [AJAX请求方式]
> 1.GET系列请求

> - get
> - delete：从服务器上删除某些资源文件
> - head：只想获取服务器返回的响应头信息（响应主体内容不需要获取）

> 2.POST系列请求
> 
> - post
> - put：向服务器中增加指定的资源文件
> 不管哪一种请求方式，客户端都可以把信息传递给服务器，服务器也可以把信息返回给客户端，只是GET系列一般获取为主（给的少拿回来的多），而POST系列一般以推送为主（给的多拿回来的少）
> 1）我们想获取一些动态展示信息，一般使用GET请求，因为只需要向服务器端发送请求，告诉服务器端告诉我们想要什么，服务器端就会把需要的数据返回
> 2）在实现注册功能的时候，我们需要把客户输入的信息发送给服务器进行存储，服务器一般返回成功还是失败等状态，此时我们一般都是一般基于post请求完成的
> 
> GET系列请求和POST系列请求，在项目实战中存在很多的区别
> 1.GET请求传递给服务器的内容一般没有POST请求传递给服务器的内容多
> 原因：GET请求传递给服务器内容一般都是基于`url地址问号传递参数`来实现的，而POST请求一般都是基于`设置请求主体`。各大浏览器都有自己URL最大长度限制（谷歌：8KB、火狐：7KB、IE：2KB...，超过限制长度的部分，浏览器会自动截取掉，导致传递给服务器的数据缺失）
> 理论上POST通过请求主体传递是没有大小限制的的，真实项目中为了保证传输的速率，我们也会限制大小（例如：上传的资料或者图片我们都会做大小限制）
> 2.GET请求很容易出现缓存（这个缓存不可控：一般我们都不需要），而POST不会出现缓存（除非自己做特殊处理）；
> 原因：GET是通过URL问号传参给服务器信息，而POST是设置请求主体；
> 设置请求主体不会出现缓存，但是URL传递参数就会了。
> GET缓存解决方案： url: 'getList?lx=news&_='+Math.random() //追加随机数，保证每一次请求url不一致
> 3.GET请求没有POST请求安全（POST也并不是十分安全，只是相对安全）
> 原因：还是因为GET是URL传参给服务器


----------

> URL：请求数据的地址（API地址），真实项目中，后台开发工程师会编写一个API文档，在API文章中汇总了哪些数据需要使用哪些地址，我们按照文档操作即可
> ASYNC：异步（SYNC同步）,设置当前AJAX请求是异步还是同步的，不写默认是异步（true），如果设置为FALSE，则代表当前请求是同步的
> 用名和密码：这两个参数一般不用，如果你请求的URL地址所在的服务器设定了访问权限，则需要我们提供可通行的用户名密码才可以（一般服务器都是可以允许匿名访问的）

###第三部分细节研究
```javascript
//=> 监听AJAX状态改变，获取响应信息（获取响应头信息，获取响应主体信息）
xhr.onreadystatechage=()=>{
    if(xhr.readyState === 4 && xhr.status === 200){
        let result = xhr.responseTesxt; //=> 获取响应主体内容
    }
}
```

> AJAX状态码：描述当前AJAX操作的状态

> xhr.readyState
> 0：UNSENT 未发送，只要创建一个AJAX对象，默认值就是零
> 1：OPENED 我们已经执行了xhr.open这个操作
> 2：HEADERS_RECEIVED 当前AJAX请求已经发送，并且已经接收到服务器端返回的响应头信息了
3：LOADING响应主体内容正在返回的路上
4：DONE响应主体内容已经返回到客户端


----------

> HTTP网络状态码：记录当前服务器返回信息的状态xhr.status来获取
> 200：成功，一个完整的http事务完成（以2开头的状态码一般都是成功）

> 以3开头的一般也是成功，只不过服务器端做了很多特殊的处理
> 301：Moved Permanently 永久转移（永久重定向），一般应用于`域名迁移`
> 302：Move temporarily 临时转移（临时重定向，新的HTTP版本中认为307叫临时重定向）`一般用于服务器的负载均衡L当前服务器处理不了，我把当前请求临时交给其他的服务器处理（一般图片请求经常出现302，很多公司都有单独的图片服务器）`
> 304：Not Modified 从浏览器缓存中获取数据`把一些不经常更新的文件或者内容缓存到服务器中，下一次从缓存中获取，减轻服务器压力，也提高页面加载速度`

> 以4开头的，一般都是失败，而且客户端的问题偏大
> 400：请求参数错误
> 401：无权限访问
> 404：访问地址不存在               
> 
> 以5开头的，一般都是失败，而且服务器的问题偏大
> 500： Internal Server Error 未知的服务器错误
> 503：Service Unavailable 服务器超负载

`Ajax中其他常用的属性和方法`

[属性]

> 面试题：AJAX中常用的方法有几个？

> - 1.readyState：存储的当前AJAX状态码（0 1 2 3 4）
> - -response/responseText/responseXML : 都是用来接收服务器返回的响应主体中的内容，只是根据服务器返回内容的格式不一样，我们使用不同的属性接收即可
>   + responseText是最常用的，接收到的结果是字符串格式的（一般服务器返回的数据都是JSON格式字符串）
>   + responseXML偶尔用到，如果服务器端返回的是XML文档数据，我们需要使用这个属性接收
> - status:记录了服务器返回的HTTP状态码
> - statusText：对返回状态码的描述
> - timeout：设置当前AJAX请求的超时时间，假设我们设置时间为3000ms，从AJAX请求发送开始，3秒后响应主体内容还没有返回，浏览器会把当前AJAX请求任务强制断开
> [方法]
> - abort():强制中断AJAX请求
> -getAllResponseHeaders():获取全部的响应头信息（获取的结果是一堆字符串文本）
> - getResponseHeader(key):获取指定属性明的响应头信息：例如：xhr.getResponseHeader('date')获取响应头中存储的服务器时间
> - open(): 打开一个URL地址
> - overrideMimeType():重写数据的MIME类型
> - send(); 发送AJAX请求（括号中写的内容是客户端基于请求主体把信息传递给服务器）
> - setRequestHeader(key,value):设置请求头信息（可以是设置自定义请求头信息）
> [事件]
> - onabort : 当AJAX被终端触发
> - onreadystatechange : AJAX状态发生改变，会触发这个事件
> - ontimeout: 当AJAX请求超时，会触发这个事件

```javascript
var xhr = new XMLHttpRequest();
    xhr.open('GET','./temp.json',true);
    xhr.setRequestHeader('aaa','xxx');//只能放到OPEND状态后和SEND前,设置内容不能为汉字
    xhr.timeout = 100;//请求超时事件限制
    xhr.ontimeout = function (){
        console.log('请求超时')
        xhr.abort();
    }
    xhr.onreadystatechange = function (){
        var state = xhr.readyState,
            status = xhr.status;
        if(!/^(2|3)\d{2}$/.test(status)) return
        if(state === 2){
            cosnole.log(xhr.getAllResponseHeaders,xhr.getResponseHeader)
        }
        if(state === 4){
            console.log(xhr.responseText)
            console.log(xhr.responseXML)
        }
    }
    xhr.send(null)//=>参数
```

###JS中常用的编码解码方法

> 正常的编码解码（非加密）
> 1.escape/unescape：主要把中文汉字进行编码和解码的(一般只有JS语言支持：一般也经常应用于前端页面通信时候的中文汉字编码)

```javascript
var str = '啊哈哈哈哈';
escape(str);//"%u554A%u54C8%u54C8%u54C8%u54C8"
unsecape("%u554A%u54C8%u54C8%u54C8%u54C8");//啊哈哈哈哈
```

> 2.encodeURI(str) | decodeURI:基本上所有的编程语言都支持
> 3.encodeURIComponent | decodeURIComponent:和第二种方式非常的类似，区别在于encodeURI不能处理特殊字符

> 也可以通过加密的方式进行编码解码
> 1.可逆转加密
> 2.不可逆转加密（一般都是基于MD5加密完成，可能会把MD5加密后的结果二次解密）

###AJAX中的同步和异步

> AJAX这个任务：发送请求接收到响应主体内容（完成一个完整的HTTP事务）
> xhr.send(): 任务开始
> xhr.readyState === 4: 任务结束

```javascript
var xhr = new XMLHttpRequest(); //0
	xhr.onreadystatechange = function (){
		console.log(xhr.readyState);
	};//放在open前面打印出1和4，同步模式开启后绑定事件不生效当xhr.readyState === 4的时候绑定事件才生效
	xhr.open('get','temp.json',false);//1
	xhr.onreadystatechange = function (){
		console.log(xhr.readyState);
	};//值打印出4
	xhr.send(null);//4
	xhr.onreadystatechange = function (){
		console.log(xhr.readyState);
	};//无值打印出，当xhr.readyState=4才绑定上方法，此时值也不会变了
```

###从服务器上拿时间

> 1. 用HEAD请求而不用GET因为不需要请求主体
> 2. 用异步AJax因为状态码为2的时候就能拿到请求头部信息，也就是服务器时间
> HEAD请求AJAX状态码没有3，因为head没有请求主体

