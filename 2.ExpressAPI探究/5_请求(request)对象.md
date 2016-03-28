

Express框架请求处理函数中的requestobject是对Node核心http.request的封装.它是客户端发送http请求的抽象.在web端,http请求一般包含以下几个部分

 1. Method: Get,Post或者Delte
 2. URL:请求资源的位置
 3. Host:重载服务的主机名,比如我要请求百度的Index首页,host就是www.baidu.com
 4. Body:主体内容,包含JSON格式或者其他formdata.（二进制流等）
 
Express框架的request对象支持所有http.request能做的,也添加了一些额外的功能,比如自动解析url的参数.（queryString）

 5. **request.query：**解析queryString的参数,www.baidu.com/products?name='Nike'
 6. **request.params：**解析url的参数,例如www.baidu.com/products/:productId
 7. **request.route：**路由的路径
 8. **request.cookie：**请求中的cookie数据,一般配合cookie-Parser中间件使用
 9. **request.body：** http请求主体的数据.一般需要配合body-Parser中间件解析
 
编写一小段程序为了更好的理解Express的request的请求对象.
首先创建package.json然后更改内容如下：
![requestApplicaitonPackage.json](http://f.hiphotos.baidu.com/image/pic/item/0d338744ebf81a4c5cb9e172d02a6059242da685.jpg)

        npm install --执行将会获得程序所有的依赖
接着就是监听端口挨个试试request的内容吧


 1. **request.query**
    request.query是处理Url问号后面的参数问题,专业术语就是查询字符串
        
        var express=require('express');
        var app=express();
        app.get('/search',function(req,res,next){
            console.log(req.query);
        }).listen(3000)

    在浏览器输入localhost:3000/search和localhost:3000/search?name=Jacky

    ![req.query](http://a.hiphotos.baidu.com/image/pic/item/a1ec08fa513d2697a9d906e652fbb2fb4316d807.jpg)

    原来request.query能够帮我们解析url查询字符串的参数.并且能够通过属性获得参数的值

        console.log(req.query['name']) --Jacky
 2. **request.paramas**
    通过Url传递参数有几种方式,可以像前面一样/products?productId=3,也可以不采取查询字符串的形式.直接通过斜杠带参数./products/:productId.

        app.get('/products/:productType/:productId',function(req,res,next){
            console.log(req.params)
        })
在浏览器输入localhost:3000/products/fruit/5.

    ![req.params](http://a.hiphotos.baidu.com/image/pic/item/b21bb051f8198618fe58e73f4ded2e738bd4e66a.jpg)

    当然你也可以改写成第一种方式/products?productType=fruit&productId=3通过request.query拿到值.具体怎么使用看你的习惯和项目需求.
 3. **request.body**
    request.body又是Express框架提供的功能强大的对象,它通常和body-parser中间件使用.
    body-parser提供了两个方法,json和urlencode,json方法用于解析http请求主体中键值对,并    转换成JSON对象.urlencode是将url的参数解析放进request.body中.

        npm install body-parser --save-dev
        
        var bodyParser=require('body-parser');
        app.use(bodyParser.json());
        app.use(bodyParser.urlencode());
        
    你不必加载两种解析方式,只要按照自己的习惯解析.一般使用json方法将请求主体的键值对    解析成JSON对象
 4. **request.route**
    request.route是一个包含下面几个属性的对象

    ***path:*** 包含了最原始的Url
    ***method:*** http请求的方法,Get,Post
    ***params:*** 就是requert.params对象 
    浏览器输入localhost:3000/products/fruit/5
    
    ![](http://g.hiphotos.baidu.com/image/pic/item/77094b36acaf2edd85f636528a1001e939019301.jpg)
 5. **request.cookies**
    request.cookies一般配合cookie-parser中间件使用.用来解析客户端浏览器一起发送过来的cookies.维持服务端和客户端会话最重要的sessionId就是存在cookie中.
    
        npm install cookie-parser --save-dev

        app.get('/cookies',function(req,res,next){
            var cookies=req.cookies['counter']
            if(!cookies) res.cookie('counter',0)
            else res.cookie('counter',parseInt(cookies)+1)
            res.json(req.cookies['counter'])
        })
    第一次检查浏览器是否带了counter字段,如果没有的话,就种一个cookie.如果有的话就在基础上递增1.并且显示在浏览器上.
    
    ![req.cookies](http://b.hiphotos.baidu.com/image/pic/item/f603918fa0ec08fa88ae761c5eee3d6d54fbdadb.jpg)
 6. **request.header和request.get方法**
    request.header和request.get方法的作用最简单就是获取http请求头的某一个字段的值.
        
        app.get('/cookies',function(req,res,next){
            console.log(req.header('Accept')) --text/html,application/xhtml+xml..
        })

其他的方法和属性就不一一赘述了,大家有兴趣的话可以翻翻Api自己摸索下.

 
    

 

