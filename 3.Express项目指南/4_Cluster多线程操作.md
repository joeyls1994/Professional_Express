

自Node问世起来,很多持怀疑态度的人都反对使用Node.js,其中最大的原因就是node的模型是基于单线程的,无法高效的利用多核CPU.这会导致服务器CPU资源的浪费.但是有了***Cluster*** 模块后,node可以很轻松的分支出多线程,并且远离了传统编程语言中状态同步和死锁的问题.

在web程序中,进程可以监听相同的端口,如果第一个进程很忙的话,http请求将会被第二个进程所处理（前提是他们监听了相同的端口）,所以我们传统上会根据机器上的CPU数目分成好几个进程.这样就能充分的利用多核CPU的优势.

##**一个多线程的例子**

下面的Express程序在四个进程上运行,其中有一个进程叫做主进程,其他的三个叫工作进程.主进程负责管理和监视工作进程的状态.在最开始的地方,我们导入项目依赖

        var express=require('express');
        var http=require('http');
        var numCpus=require('os').cpus().length;
        var cluster=require('cluster'); 
        
cluster模块将内置一个属性告诉我们进程是主进程还是工作进程.我们可以利用这个属性分出和CPU数目相同的工作进程数,而且我们还可以在主进程中订阅事件来管理和监视工作进程.

        if(cluster.isMaster){
            console.log('Fork %s works from master',numCpus);
            for(var i=0;i<numCpus;i++){
                cluster.fork();
            };
            cluster.on('online',function(worker){
                console.log('Worker is running on %s pid',worker.process.pid);
            });
            cluster.on('exit',function(worker){
                console.log('Worker with %s is closed',worker.process.pid);
            });
        }
        else if(cluster.isWorker){
            //原有的express程序代码
            var app=express();
            app.get('*',function(req,res,next){
                res.status(200).send('Cluster '+cluster.worker.process.pid+' responsed');
            })
            app.listen(3000)
        }
        
启动Node服务器后,你会发现主进程会根据当前CPU的数目分支成多个进程.我的电脑是8核的.所以run的结果如下.

![cluster-worker](http://g.hiphotos.baidu.com/image/pic/item/79f0f736afc379312c317f89ecc4b74543a91130.jpg)

系统会自动分配pid,它们监听着相同的端口.打开任务管理器,你也会发现有9个node-server的进程.

![taskmanager](http://d.hiphotos.baidu.com/image/pic/item/b8014a90f603738d9af6f582b41bb051f919ecd6.jpg)

因为主进程订阅了wroker的退出事件,如果你强行结束其中一个worker进程的话,会看到控制台的提示.

![cluster-workerWithClosed](http://c.hiphotos.baidu.com/image/pic/item/7c1ed21b0ef41bd5afa5115456da81cb38db3d9f.jpg)

我们可以把整个express程序中写在cluster.isWorker语句里面,但更多时候,我们会把express的程序封装成一个app文件.然后用文件模块的方式导入到cluster的文件中.

        var cluster=require('cluster');
        var numCpus=require('os'),cpus().length;
        var app=require('./app.js') //导入封装了express程序的文件模块
        
        if(cluster.isMaster){
            console.log('Fork %s works from master',numCpus);
            for(var i=0;i<numCpus;i++){
                cluster.fork();
            };
            cluster.on('online',function(worker){
                console.log('Worker is running on %s pid',worker.process.pid);
            });
            cluster.on('exit',function(worker){
                console.log('Worker with %s is closed',worker.process.pid);
            });
        }else if(cluster.isWorker){
            app.listen(3000);
        }