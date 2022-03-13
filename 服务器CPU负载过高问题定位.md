# 服务器CPU负载过高问题查询记录

## 开始查看服务器：

### 1：通过top服务器，定位到PID为30284的jvm进程占用CPU到达100%

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/25/16aee40533cca324~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 2：显示线程列表，按照CPU占用高的线程排序

[admin@xxxxx]#  top -Hp  30284

### 3：找到最耗时线程的PID，然后将它转成16进制的格式

[admin@xxxxx]#  printf "%x\n"  31448 77e0

### 4：通过jstack命令查看jvm，grep耗时线程的详细堆栈信息

[admin@xxxx] # jstack 30284 |  grep 77e0 -A  60

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/25/16aee40533b3269e~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)