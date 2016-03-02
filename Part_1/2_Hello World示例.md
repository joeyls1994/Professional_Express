##基础开始

废话少说,让我们直接开始吧，首先创建一个ProExpressProject文件夹,并在子目录中创建1_2文件夹代表第一部分的第二章节.
在1_2文件夹中创建hello.js作为程序的主入口.

		var express=require('express');  
		var app=express();  
		var port=3000;  
		app.get('*',function(req,res){
			res.end('hello world');  
		})  
		app.listen(port,function(){
			console.log('run successful at port %s',port)  
		});  

这是打开浏览器输入localhost:3000的话,会看到浏览器输出了hello world,控制台输出了run successful at port 3000.
app.get('*')是通配符,所有从客户端请求的url都会被匹配,向终端输出hello world字符串.

不管怎么样,这是一个好的开始,至少我们看到了HelloWorld.接下来为了增加一点交互性,我们添加额外的路由,让程序变得高级一点.

		var express=require('express');  
		var app=express();  
		var port=3000;  
		app.get('/name/:user_name',function(req,res){
			res.status(200);
			res.set('Content-Type','text/html');
			res.end('<html><body>' +
			'<h1>Hello ' + req.params.user_name + '</h1>' +
			'</body></html>')
		})
		app.get('*',function(req,res){
			res.end('hello world');  
		})  
		app.listen(port,function(){
			console.log('run successful at port %s',port)  
		});  

这里又增加了一个路由,路径name并且后面带参数的url将会被分发到新添加的路由上,（下面的路由也被分发到了,为什么没有执行呢，等会再说）.总之在浏览器输入localhost:3000/name/Jacky,会渲染出Hello Jacky的界面.
现在我们说说为什么两个路由都能匹配到request,却执行了第一个匹配name的路由.答案很简单,因为程序是从上向下执行的,谁在最上面就最先执行,最先执行的路由捕获了request后能成功返回响应的话,后面的路由就算匹配也不会执行了.

最先执行的路由不返回响应并且也没有next方法的话,就阻塞住.添加next方法就会跳到下一个匹配的路由上继续执行.所有路由遵循这样的原则.

		var express=require('express');  
		var app=express();  
		var port=3000;  
		app.get('/name/:user_name',function(req,res,next){
			res.status(200);
			res.set('Content-Type','text/html');
			next();
		})
		app.get('*',function(req,res){
			res.end('hello world');  
		})  
		app.listen(port,function(){
			console.log('run successful at port %s',port)  
		});   

再次输入localhost:3000/name/Jacky会返回hello world,这就是我刚才说的那个情况,经过第一个路由（/name/Jacky）的时候,仅仅是添加了状态码和响应头,有next方法,继续向下执行,再次匹配到第二个路由（*）,有res.end方法,成功返回响应.

##脚手架命令行

前面我们已经安装了express生成命令模块,express-generator,进入项目目录后输入

		express ProjectName  

![express-generator](http://b.hiphotos.baidu.com/image/pic/item/a08b87d6277f9e2fb63f775f1830e924b899f35b.jpg)
这里创建的文件名是Server.命令行工具会自动创建一些基本的express的项目文件.

现在我们来仔细看下express项目的文件结构.

![file-structure](http://mt1.baidu.com/timg?shitu&quality=100&sharpen=100&er=&imgtype=0&wh_rate=null&size=9&sec=1456901892&di=bab55822e14b14879bc71e86521574f0&cut_x=2&cut_y=4&cut_w=243&cut_h=330&src=http%3A%2F%2Fd.hiphotos.baidu.com%2Fimage%2Fpic%2Fitem%2Fa71ea8d3fd1f4134dc425229221f95cad1c85e6b.jpg)

###public
文件夹下面放了一些静态资源,类似CSS,JS还有Image这样的文件.
###views
文件夹下面放了一些供渲染的jade模板,可以被jade渲染成html文件.
###routers
文件夹下面放了一些路由处理文件.
###app.js
文件是主文件,程序的入口.