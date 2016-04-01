
本节主要讲解的是Express两大主题,缓存和用户权限控制.
Redis是一个非常快速高效的缓存数据库经常配合session达到用户会话持久化的功能.

##**Redis缓存**
Redis缓存通常和session搭配应用在Express的项目上,储存在物理内容中来避免用户的刷新和重部署导致的数据丢失.多个RESTful的服务器可以共享一个Redis缓存,只要连接的是相同的Redis服务器就好了.
Redis是一个独立的服务,如果嵌入到Express程序需要做两件事:

 - ***Redis的服务器,用于存缓存数据.***
 - ***Connect-Redis模块中间件,用于Express程序和Redis服务器的通讯.***

