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

###public
文件夹下面放了一些静态资源,类似CSS,JS还有Image这样的文件.
###views
文件夹下面放了一些供渲染的jade模板,可以被jade渲染成html文件.
###routers
文件夹下面放了一些路由处理文件.
###app.js
文件是主文件,程序的入口.

##app.js
打开app.js文件,发现express-generator已经生成了一些基本代码.

		var express = require('express');
		var path = require('path');
		var favicon = require('serve-favicon');
		var logger = require('morgan');
		var cookieParser = require('cookie-parser');
		var bodyParser = require('body-parser');

		var routes = require('./routes/index');
		var users = require('./routes/users');	

以上就是主入口文件的加载模块阶段,稍微细分一下,模块可以分成三大类,node自带的核心模块,比如说path,node_modules模块,这个是通过npm下载的,比如express,body-parser.还有就是文件模块,类似index,users文件.文件系统因为存在module.exports,照样可以按照CommonJS规范当成模块使用.	

		var app=express();  
实例化阶段.生成了一个express的对象赋值给app.

		app.set('views', path.join(__dirname, 'views'));  
		app.set('view engine', 'jade');  
设置模板的路径和模板渲染引擎.

		app.use(logger('dev'));
		app.use(bodyParser.json());
		app.use(bodyParser.urlencoded({ extended: false }));
		app.use(cookieParser());
		app.use(express.static(path.join(__dirname, 'public')));  

中间件,解析body和cookie,请求静态资源直接去public文件夹里面找.

		app.use('/', routes);
		app.use('/users', users);  

路由分发阶段,'/'会被拿到index.js文件去处理,'users'会被拿到users文件去处理.

		app.use(function(req, res, next) {
			var err = new Error('Not Found');
			err.status = 404;
			next(err);
		});


		if (app.get('env') === 'development') {
			app.use(function(err, req, res, next) {		
				res.status(err.status || 500);
				res.render('error', {
					message: err.message,
					error: err
				});
			});
		}
		app.use(function(err, req, res, next) {
			res.status(err.status || 500);
			res.render('error', {
				message: err.message,
				error: {}
			});
		});


错误处理阶段,应该要放在最后面.


##文件MVC结构和模块
express框架是非常容易配置和扩展的,按理来说没有什么固定的架构,但是一般来说我们会按照下面的约定来组织文件的摆放.

config文件大家可能比较熟悉,就是存放项目配置的地方,models其实是存在数据库Schema的地方,稍后讲到mongo数据库和express配合使用的时候会提到.

![fileStructure](http://d.hiphotos.baidu.com/image/pic/item/a6efce1b9d16fdfabccd86b9b38f8c5495ee7bc7.jpg)

##监视文件的变化

Express的代码运行在node服务端,存储在内存中,一旦我们更改了源代码,就需要切断进程然后重新启动,每次都要Ctrl+C,然后node app.js.这显然是一件非常繁琐的事情.

其实自动重启服务器的插件还是很多.在这里就介绍一种.
###supervisor插件
全局安装supervisor模块.

		npm install -g supervisor  
修改package.json中start里的参数
node ./bin/www 改成supervisor ./bin/www.

不是通过npm start启动的用户可以直接用supervisor app.js取代node app.js.