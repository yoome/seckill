# seckill-dubbo

## 项目介绍


该项目为基于Dubbo创建的分布式秒杀系统。

**主要内容：**

- 针对瞬时访问量大的情况，我们通过横向增加服务的数量方式解决；2. 秒杀时，会存在大量的数据库读写，此时数据库将成为影响全局性能的瓶颈，
我们可以通过只让最终秒杀成功的有限用户进行数据库写操作来优化；3. 如果秒杀的商品数量庞大时，仍需进行大量的数据库读写，这个时候，我们需要把大量的读写操
作放到消息队列中异步处理，数据库可以"慢慢"的读写，分担短时间的处理量。

- 针对并发量很大带来的流量很大的问题，我们可以给浏览器做静态缓存，页面静态化，服务只返回访问所需要的数据而不是整个页面；或者在CDN部署页面。 

- 针对利用脚本，或者过于频繁秒杀的操作，服务端需要做防刷处理，限定单位时间的秒杀次数；对于秒杀接口，采取隐藏秒杀接口的处理，只有在秒杀进行时，才暴露
秒杀接口。 

- 针对可用性与分布式容错，我们需要双机热备，利用keepalived+Nignx做负载均衡，然后利用Dubbo框架接着实现负载均衡与容错处理。 


**项目特性：** 
- 秒杀的时候系统的并发量会非常的大，系统要在短短的几秒内完成非常大的访问处理。 

- 并发量大的同时，网络的流量也会瞬间变的很大。 

- 秒杀项目特性下，可能会存在安全性问题，1.可能会存在脚本来进行秒杀，2.可能会提前得到秒杀接口，影响秒杀公平性。 

- 项目要具备CAP中的AP,可用性与分布式容错，保证系统服务可用。



**特性：**

- 利用页面静态化，CDN缓存，Nignx缓存，以及服务端的页面缓存，Redis对象缓存，层层过滤，防止访问直接穿透到DB。

- 假如实际秒杀产品很多，最终过滤后还有大量的数据库读写，这时采用消息队列，异步处理；浏览器只需要轮询秒杀结果就可以了，这样可以把短时间的大量数据库操作
分担的相对较长的时间，保证数据库可用。

- 秒杀过程中，需要保证可用性，所以需要对Nignx进行水平扩展，双机热备份；服务提供方也需要按照实际情况水平扩展。

**注意事项：**

- 需要对[数据库填充初始数据](https://github.com/wtiscm/seckill-dubbo/blob/master/doc/miaosha.sql) 。

- [配置Zookeeper](https://github.com/wtiscm/seckill-dubbo/blob/master/doc/Zookeeper.md)。

- [配置Redis](https://github.com/wtiscm/seckill-dubbo/blob/master/doc/Redis.md)。

- [配置RabbitMQ](https://github.com/wtiscm/seckill-dubbo/blob/master/doc/RabbitMQ.md)。

**项目启动过程：**

- 启动MySQL,Redis,Zookeeper,RabbitMQ。
- 需要启动6个服务，注册的Zookeeper注册中心。如果使用IDEA，分别启动 seckill-cache,seckill-goods,seckill-user,seckill-miaosha,seckill-mq,seckill-order 6个
  提供方服务;然后启动seckill-controller 服务消费方。 或者把对应的服务提供方，与服务消费方打成jar包，以jar包的形式启动。

- http://localhost:8091/seckill/to_login  用户名：13512341234，密码：123456，进行登陆。


**引用：**
>  <http://dubbo.apache.org/en-us/index.html>  
>  <https://github.com/apache/dubbo>  
>  <https://www.keepalived.org/>  
>  <https://zookeeper.apache.org/>  
>  <http://nginx.org/en/docs/>  
>  <https://redis.io/>  
>  <https://coding.imooc.com/class/168.html>  
>  <https://blog.csdn.net/jokeMqc/article/details/87933573>  
>  <https://www.rabbitmq.com/>  
