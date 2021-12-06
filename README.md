- docker-compose 1.17以下版本无法正确的限制docker内存和cpu使用，docker stats还是无限制状态
> 这是一个Docker Bug，已提交issue，之后版本无此类问题

- docker stop 无法优雅停止，指定-t也超时
> 首先Linux版本bash方式 ubuntu下：/bin/sh -> dash，在centos下：/bin/sh -> bash。CMD或者ENTRYPOINT （command param1 param2） 使用shell 格式，默认/bin/sh -c ，要是用[“executable”, “param1”, “param2”] exec 使用 就没问题，启动的时候如果 CMD带了变量 ，需要指定SHELL [“/bin/bash”, “-c”]。 还需程序做好信号处理。

- 网络问题，无法pull和push
> 使用阿里云或者其他第三方代理等

- 容器运行多个程序需求
> 最好使用supervisord ，但不建议一个容器运行多个进程

- 使用内存和 swap 限制启动容器时候报警告：”WARNING: Your kernel does not support cgroup swap limit. WARNING: Your kernel does not support swap limit capabilities. Limitation discarded.”？
>这是因为系统默认没有开启对内存和 swap 使用的统计功能，引入该功能会带来性能的下降。要开启该功能，可以采取如下操作：  
  编辑 /etc/default/grub 文件（Ubuntu 系统为例），配置 GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"  
  更新 grub：$ sudo update-grub  
  重启系统，即可。  

- docker 缓存问题
> 先docker build —no-cache
在执行 docker stop 在执行docker-compose up

- docker-compose healthcheck和depends_on 检查顺利启动不依赖问题
> 需要在dockefile添加 ENTRYPOINT /go/wait-for.sh 127.0.0.1:22530 — run  
检查需要依赖的主程序22530是否启动成功，然后在启动本身就不会报错了。  
wai-for.sh URL：https://github.com/vishnubob/wait-for-it
