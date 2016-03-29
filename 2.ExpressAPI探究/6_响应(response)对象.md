
Express的response对象是请求处理函数中的参数,它和request对象一样,是Node核心http.response对象的封装.因为Express的response对象有了新的方法,某种程度上来说,它是http.response对象的扩展.

为什么要添加一些额外的方法呢？你可以写一些原生的核心方法,但意味着你要写比以前更多的代码量.你可能要写writeHead方法,设置200或者404状态码等.用了express框架以后,只要做简单的封装即可.res.json或者res.send方法,响应头会自动添加上去.下面就看看response对象的方法和属性

 - **res.render()**
 - **res.locals**
 - **res.set()**
 - **res.status()**
 - **res.send()**
 - **res.json()**
 - **res.jsonp()**
 - **res.redirect()**

为了更好的讲解这些方法我们还是和以前一样,run一个小程序.

###**res.render(name,data,callback)**
render方法是express框架的精髓,你可能也会猜到,它是express框架调用引擎渲染模板的方法.它一般会接受三个参数,但只有第一个参数是必须的.另外两个参数是可选的.name是要渲染的模板的名称,如果已经定义了默认模板引擎,名称可以不带后缀名.data是渲染时传递给前台html页面的数据.callback是渲染成功后的回调函数.

    在app.js的入口文件中定义路由
        app.get('/render',function(req,res,next){
            res.render('render');
        });

在views下面的文件夹添加一个render.jade模板.编写以下内容.注意缩进格式,jade模板对标签的缩进还是要求严格的.

![](http://b.hiphotos.baidu.com/image/pic/item/f9dcd100baa1cd11235f2a9cbe12c8fcc2ce2da4.jpg)

打开浏览器输入localhost:3000/render

![response-browser](http://h.hiphotos.baidu.com/image/pic/item/a50f4bfbfbedab647c18167cf036afc379311e27.jpg)


除了必要的name参数,还有两个可选参数,data和callback.data参数会带数据过去,这让页面变得更加动态.改变data的值也会更新页面数据的表现.重写上面的例子让页面不再是静态页面.

        app.get('/render',function(req,res,next){
            res.render('render',{title:'Professional Express'});
        });

因为引擎在渲染模板的时候还传递了参数,这个时候就需要修改模板用title变量来表示后端传递的数据,也可以解释成占位符.

![responseWithParameters](http://h.hiphotos.baidu.com/image/pic/item/0b7b02087bf40ad1cd2a9c5f502c11dfa8ecce44.jpg)

这样页面中的#{title}会被render传递的数据字符串'Professional Express'取代

![responseBrowserWithDataParameters](http://d.hiphotos.baidu.com/image/pic/item/d788d43f8794a4c226a6bd9709f41bd5ac6e3980.jpg)
callback参数自带两个参数,error和渲染成功后要输出的模板（Node的错误参数一般放在第一个参数的位置）.

        app.get('/render',function(req,res,next){
            res.render('render',{title:'Professional Express'},function(err,html){
                //do something
            })
        })
        
如果没有数据需要传递的话,第二个参数也可以摆回调函数,因为express框架是判断参数的类型来分配任务的.

        app.get('/render',function(req,res,next){
            res.render('render',function(err,html){
                // do something
            })
        })
        
在Express的框架底层,当render方法解析成功或者失败的时候都会调用res.send方法.如果没有指定回调函数的话,express也会执行默认的回调函数

        fn=fn||function(err,html){
            if(err) return next(err)
            self.send(html)
        }

当执行了回调函数就执行回调函数,当没指定的话就执行默认的回调函数.

###**res.locals**

res.locals是另外一个可以传递数据的对象.你应该知道第一种传递数据给模板的方式了,没错,就是render方法的第二个参数.

        app.get('/render',function(req,res,next){
            res.render('render',{title:'Professional Express'});
        });

res.locals同样可以达到这样的目的,res.locals对象可以在模板内部被访问到.

        app.get('/render',function(){
            res.locals={title:'Professional Express'};
            res.render('render');
        });
        
修改app.js的入口文件的路由请求处理函数

![res.locals](http://d.hiphotos.baidu.com/image/pic/item/f3d3572c11dfa9ec1953ecab65d0f703918fc109.jpg)

![](http://c.hiphotos.baidu.com/image/pic/item/738b4710b912c8fcf1ee5371fb039245d6882168.jpg)

都完成了同样的事情,那么res.locals的优势是什么？res.locals原来可以定义在中间件上.我们可以暴露信息在中间件里,可以被稍微的http请求处理.

![res.localsInMiddleWare](http://a.hiphotos.baidu.com/image/pic/item/9358d109b3de9c82c1a35c336b81800a18d84343.jpg)

![res.localsTemplate](http://f.hiphotos.baidu.com/image/pic/item/c2fdfc039245d688e1d61518a3c27d1ed31b24b1.jpg)

###**res.set()**

res.set是res.header方法的另一种实现,同时也是Node的http.setHeader方法的封装.不同点就是set方法能解析以对象的方式传递多个header的设置.

        app.get('/get-html',function(req,res,next){
            res.set('Content-Type','text/html');
            res.end('<html><body>' +
                    '<h1>Express.js Guide</h1>' +
                    '</body></html>')
        });
        
res.set('Content-Type','text/html')告诉浏览器服务器想要返回的是html页面.最后返回了一串html的字符串才能正确的被浏览器解析.
你可以查看响应头多了一个'Content-Type:text/html'字段.也可以传递一个对象包含多个响应头设置的键值对!![res-set](http://c.hiphotos.baidu.com/image/pic/item/ac6eddc451da81cb03e32e715566d01609243100.jpg)

在浏览器输入localhost:3000/get-html后检查响应头

![res-setCookie](http://c.hiphotos.baidu.com/image/pic/item/4610b912c8fcc3cee9e0658f9545d688d43f206d.jpg)

如果你用res.send方法的话,就算不加上头Express框架也会自动补上,但是res.end不会自动补上响应头.

###**res.status()**

res.status方法将返回这次请求的http的状态码,下面是一些常用的http状态码.

 - ***200：***请求成功
 - ***301：***请求的资源永远的移动到了新位置
 - ***401：***没有权限请求资源
 - ***404：***请求的资源不存在,没找到
 - ***500：***服务器端发生错误
 
res.status返回的依然是res对象,这就说明res.status支持链式编程.

        app.get('/getCode',function(req,res,next){
           res.status(200).send({message:'OK!'}); 
        });
        
###**res.send()**
res.send方法可以返回任何数据格式,并且能根据传入的数据类型自动生成http响应头.

 - ***String：*** res.send("I'm string"); **text/html**
 - ***Object：*** res.send({message:"OK!"}); **application/json**
 - ***Array：*** res.send([{message:"OK"},{name:"Jacky"}]); **application/json**
 - ***Buffer：*** res.send(new Buffer('Just do it')); **application/octet-stream**

上面的类型举例的话就用Buffer好了,其他的类型大家平常见得很多了.

        app.get('/buffer-Stream',function(req,res,next){
            res.send(new Buffer('just do it'));
        })
        
![res.sendHeaderStream](http://h.hiphotos.baidu.com/image/pic/item/cf1b9d16fdfaaf51145716038b5494eef11f7ad2.jpg)


**注：如果你在res.send之前显式的设置了头,其会覆盖res.send根据传递内容自动生成的Http响应头**

###**res.json()**

当res.send方法返回的是Json格式的数据时,它和res.json是一样的,换句话说,res.json要求传递的内容必须是Json格式的对象.所以默认的Content-Type是application/json.当然你也可以用res.set方法要重写响应头信息.

###**res.jsonp()**
res.jsonp和res.json方法类似,只不过jsonp提供了一个jsonp的返回,将json格式的数据封装在一个函数里面.jsonp一般是解决跨域问题.但是有一点必须注意,jsonp只能用get方法请求数据.不能用post数据.

###**res.redirect()**
有些时候我们需要验证用户的权限,当验证成功时,我们才重定向到指定的路由.

        app.get('/login',function(req,res,next){
            //do something with Auth,if success
            res.redirect('/success')
        })
下面我们看看怎么是重定向的.
![redirectToRender](http://h.hiphotos.baidu.com/image/pic/item/91ef76c6a7efce1b42115a76a851f3deb48f6550.jpg)

请求/buffer-Stream,路由请求处理函数立马重定向到/render后渲染render页面.

![redirectStatusCode](http://d.hiphotos.baidu.com/image/pic/item/4610b912c8fcc3ced8d1548f9545d688d43f207c.jpg)

默认的重定向将返回302状态码.但是你也可以用redirect的第一个参数来修改返回的状态码.


**还是和request对象一样,有关response对象的其他方法和属性,大家有兴趣可以自行摸索.**

