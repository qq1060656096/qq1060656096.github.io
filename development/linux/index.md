
##

```
为什么ssh断开后你运行的进程会退出呢?
  因为所有进程都得有个父进程。当你ssh到一个服务器上时,打开的shell就是你所有执行命令的父进程.当你断开ssh连接时,
  你的命令的父进程就没了.如果处理不当,这些进程就会收到SIGTERM信号,全被干掉了.
  
  然后说解决方案：让你运行的进程的父进程变成PID=1的init进程,这样你的shell退出后不影响这些命令的继续执行.
  方法有很多：
  1.命令执行时按ctrl+z将进程暂停,然后输入bg将进程放到后台(相当于执行命令时加上&),bg输入完成后会有提示该命令的job ID,
  然后用disown命令将其父进程ID设置为1.(其实这个时候不需要disown,直接exit退出当前shell,默认此时就会将当前shell
  所有子进程的父进程设置为1)找个父进程为1的进程，做为你要后台持续运行进程的父进程.
  最简单就是nohup.但是如果你的进程需要tty交互,那最好是拿screen、tmux之类的执行.这样你运行命令的父进程(或祖先进程)
  是screen、tmux、nohup,你的ssh中的shell退出就不影响命令继续运行了



Nohup命令
每个开发者都会躺过这个坑,在命令行跑一个后台程序,关闭终端后发现进程也退出了,网上搜一下发现要用nohup,究竟什么原因呢？

原来普通进程运行时默认会绑定TTY(虚拟终端),关闭终端后系统会给上面所有进程发送TERM信号,这时普通进程也就退出了当.
然还有些进程不会退出,这就是后面将会提到的守护进程.

Nohup的原理也很简单,终端关闭后会给此终端下的每一个进程发送SIGHUP信号,而使用nohup运行的进程则会忽略这个信号,
因此终端关闭后进程也不会退出.


https://www.ibm.com/developerworks/cn/linux/l-cn-nohup/
https://www.zhihu.com/question/20709809
```