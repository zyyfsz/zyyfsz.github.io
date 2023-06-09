# Windows下如何查看某个端口被谁占用
开发时经常遇到端口被占用的情况，这个时候我们就需要找出被占用端口的程序，然后结束它，本文为大家介绍如何查找被占用的端口。

## 1、打开命令窗口(以管理员身份运行)
**开始—->运行—->cmd，或者是 window+R 组合键，调出命令窗口。**
![](https://user-images.githubusercontent.com/130021276/230584850-2b0f157f-7cb6-43b7-9942-caf01956b052.png)

## 2、查找所有运行的端口
输入命令：

```netstat -ano ```

该命令列出所有端口的使用情况。
在列表中我们观察被占用的端口，比如是 1224，首先找到它。
![](https://user-images.githubusercontent.com/130021276/230584931-7f773af9-b43a-4f07-8cf3-e66bc686c441.png)

## 3、查看被占用端口对应的 PID
输入命令：

```netstat -aon|findstr "8081" ```

回车执行该命令，最后一位数字就是 PID, 这里是 9088。
![](https://user-images.githubusercontent.com/130021276/230585030-4f6f1188-7588-4a1c-afae-69f61b39d59c.png)

## 4、查看指定 PID 的进程
继续输入命令：

```tasklist|findstr "9088" ```

回车执行该命令。
查看是哪个进程或者程序占用了 8081 端口，结果是：node.exe。
![](https://user-images.githubusercontent.com/130021276/230585075-c897523c-d5a3-4fb9-b2c3-50652c59b36e.png)

## 5、结束进程
强制（/F参数）杀死 pid 为 9088 的所有进程包括子进程（/T参数）：

```taskkill /T /F /PID 9088 ```

或者是我们打开任务管理器，切换到进程选项卡，在PID一列查看9088对应的进程是谁，如果看不到PID这一列,如下图：
![](https://user-images.githubusercontent.com/130021276/230585227-dd98ca86-af31-4fd5-8981-b8b06e370d84.png)

之后我们就可以结束掉这个进程，这样我们就可以释放该端口来使用了。
