
本节主要讲解的是Express两大主题,缓存和用户权限控制.
Redis是一个非常快速高效的缓存数据库经常配合session达到用户会话持久化的功能.

##**Redis缓存**
Redis缓存通常和session搭配应用在Express的项目上,储存在物理内容中来避免用户的刷新和重部署导致的数据丢失.多个RESTful的服务器可以共享一个Redis缓存,只要连接的是相同的Redis服务器就好了.
Redis是一个独立的服务,如果嵌入到Express程序需要做两件事:

 - ***Redis的服务器,用于存缓存数据.***
 - ***Connect-Redis模块中间件,用于Express程序和Redis服务器的通讯.***

怎么安装Redis服务器就不说了,现在run个程序把Redis缓存服务器和Express结合起来.先看package.json项目需要的依赖模块.

![express-session-package.json](http://a.hiphotos.baidu.com/image/pic/item/f2deb48f8c5494ee687ecfa62af5e0fe99257e35.jpg)

![express-session-app](http://b.hiphotos.baidu.com/image/pic/item/810a19d8bc3eb13582bc7404a11ea8d3fd1f4412.jpg)

需要注意的是,Redis的数据库名称接受的是数字.session(option)的参数前面已经讲过了,这里不多说了.store是session要存的数据库,这里是Redis缓存数据库,也可以是mongo数据库.

首先服务器会判断用户是否是第一次登录（浏览器发送的cookie是否有sessionId）,如果是第一次登录在返回的时候会有一个set-Cookie字段,设置sessionId,sessionId是存在cookie中的,每次随浏览器的请求一起发送到服务器上.
路由请求处理函数先拿到session,判断是否是第一次,如果是第一次session没有count字段,就默认设置成1储存在Redis数据库中.以后只要是相同的user(具有相同的sessionid的),都会根据sessionId去缓存系统中找count的值,找到了就递增1继续储存在数据库中.

打开浏览器输入localhost:3000后,每次刷新页面count都会递增1.count的值存在Redis中,直到session过期.

![redis-count](http://c.hiphotos.baidu.com/image/pic/item/6c224f4a20a4462333376add9f22720e0df3d794.jpg)

Redis支持四中数据类型,字符串,列表,set集合,哈希数值.

##**验证权限的方法**

通常情况验证权限的方法就是需要用户提供一个账户密码然后和数据库匹配,如果找到了记录就在session里面设置一个标志（比如authenticated）为true,Express会为了每次其他的请求储存session数据.

        app.use(function(req,res,next){
            if(req.session&&req.session.authenticated) return next();
            res.redirect('/login');
        })
        
如果我们需要用户的额外信息,比如用户名和昵称等,我们可以将对象存在session中,以后不用请求数据库了,直接在session拿昵称信息.
        
        app.post('/login',function(req,res,next){
            db.findOne({username:username,password:password},function(err,user){
                if(err) return next(new Error('something error'))
                if(!user) return next(new Error('not this user'))
                req.session.user=user;
                res.redirect('/loginSuccess');
            });
        });
        
一般用户的密码不会明文发送,而是加密后存在数据库中的.





