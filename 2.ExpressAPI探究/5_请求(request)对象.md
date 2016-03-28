

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

