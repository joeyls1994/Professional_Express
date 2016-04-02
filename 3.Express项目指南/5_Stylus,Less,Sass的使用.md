

在现代web应用中,使用CSS样式表是不可缺少的.但是在大型项目中,CSS样式表很难管理和书写,究其原因就是CSS不是一门编程语言,没有变量和函数可言,纯的CSS源码将变得非常难维护.但是多项目之间共享CSS样式依然是趋势了.

一个解决方案就是利用动态样式语言引擎,它类似CSS语法但是提供了变量和函数,能够在Build的时候编程成CSS样式表.
在Express中是用中间件实现的,当用户请求一个动态样式语言编写的文件时,Express框架会转换成纯的CSS代码返回给客户端.这样的动态样式语言有很多,比较流行的有Stylus,Sass,Less.它们使CSS可以混合,可扩展,提高了CSS的复用率和开发的效率.

这里不讲解Stylus,Less的语法,只简单说明如何在Express项目中注册中间件达到编译动态样式语言成纯的CSS.

##**Less**
首先在Express中,Less中间件封装在less-middleware模块中.
    
        npm install less-middleware --save
        
在public下面的stylesheets文件夹下面编写style.less

        @color:#4D926F
        #header{
            color:@color;
        }
        h1{
            color:@color;
        }
        
打开app.js,设置源文件路径,这里是根目录里的public文件.注:设置public实际是寻找public文件夹下面stylesheets文件件下面的文件.jade模板是要加载public/stylesheets/style.css,那么less-middleware则找public/stylesheets/style.less.
        
    app.js
    
        var lessMiddleWare=require('less-middleware');
        app.use(lessMiddleWare(path.join(__dirname,'public')));
        app.use(express.static(path.join(__dirname,'public')));
        
        app.get('*',function(req,res,next){
            res.render('index',{title:'Use less'});
        })
        
    layout.jade
    
        doctype html
        html
            head
                title=title
                link(rel='stylesheet',href='/stylesheets/style.css')
                
加载后我们会发现原来public/stylesheets文件夹下面多了一个style.css（还有编译前的style.less文件）.说明成功加载了.
        
        
        