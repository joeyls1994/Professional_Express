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
