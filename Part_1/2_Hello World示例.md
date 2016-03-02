##基础开始

废话少说,让我们直接开始吧，首先创建一个ProExpressProject文件夹,并在子目录中创建1_2文件夹代表第一部分的第二章节.
创建hello.js作为程序的主入口.

		var express=require('express');  
		var app=express();  
		var port=3000;  
		app.get('*',function(req,res){
			res.end('hello world');  
		})  
		app.listen(port,function(){
			console.log('run successful at port %s',port)  
		});  

这是打开浏览器输入localhost:3000的话,会看到浏览器输出了hello world,控制台输出了run successful at port 3000