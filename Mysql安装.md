# Mysql install for windows11

## 2022/05/21

## 在 Mysql 官网进行下载[download](https://dev.mysql.com/downloads/installer/).

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img[P6~~WC41SSZH_YXUG96T[V.png)
下一步我们直接进入下载点击**no thanks，just start my download**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521214245.png)
选择自己的下载工具，这里我推荐 [XDown](https://www.xdown.org/)工具
打开安装包**mysql-installer-community-8.0.29.0.msi**,可能过程需要等一会，需根据自己电脑配置来看等待时间。
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521215015.png)
成功进入安装界面
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521215200.png)
对这些选项解释一下
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521215634.png)
这里我们选择自定义安装**Custom**
我们先安装 Mysql Server 旁边的**加号**
点完为止
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521220717.png)
然后将鼠标定位到具体的**MySQL Server8.0.29-x64**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521220430.png)下面两个也是同理操作就不具体演示了，直接上图
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521221130.png)
同时在这个界面中，还可以查看"MySQL Server 5.7.21-X64"默认安装路径。
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521221500.png)
弹出的对话框时路径选择，你可以根据自己的需求选择路径，这里我就不改了，由于我只有 C 盘
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521221933.png)
选择完毕点击**next**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521221209.png)
然后点击执行**Execute**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521221315.png)
显示正在安装**installing**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521222201.png)
等待一会就可以
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521222428.png)
点击下一步**next**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521222559.png)
然后继续下一步**next**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521222815.png)
这里我们保持默认，可以勾选底部的选项，然后继续下一步**next**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521222746.png)
我们继续保持默认选项，继续下一步**next**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521223118.png)
设置**root**密码，密码一定要自己记住，后面需要
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521223343.png)
你也可以添加用户，我这里选择添加用户
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521223837.png)
只需要设置用户名，和密码即可
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521223343.png)
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521223756.png)
操作好后继续下一步**next**，下面修改的是 mysql 在 windows 中显示的服务名，根据自己的需求更改，我就不改了了。
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521224351.png)
继续下一步**next**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521224602.png)
接下来是一些日志文件，不做更改，点击下一步**next**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521224745.png)
在**Advanced Options**选项中直接点击下一步**next**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521224831.png)
在**Apply Configuration**中点击下一步**next**即可
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521225024.png)
完成之后点击**Finish**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521225225.png)
在**Product Configuration**中点击下一步**next**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521225252.png)
在**Connect To Server**页面，我们根据提示输入**root 密码**，再点击**Check**，密码成功会出现**Connection successed**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521225523.png)
然后我们继续下一步**next**，在出现的页面执行**Execute**等待执行完成点击**Finish**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521225900.png)
在下面几个页面一直点击下一步即可**next**，最后点击**Finish**就可以了
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521230041.png)

## 系统环境变量配置

### 目的是让 mysql 实现全局

在开始菜单中搜索**mysql**然后选中，鼠标右击，在下拉列表选择打开文件位置
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521230437.png)
继续鼠标右键打开文件位置
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521230607.png)

进入目录后，直接复制上面路径即可
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521230837.png)

## 进入环境变量

### 有很多方法可以进入

我这里就使用开始菜单搜索，直接单击进入
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521231020.png)
在弹出对话框中单击**环境变量**进入
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521231102.png)
这里我们在**系统变量**下单击**新建**
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521231240.png)
我们依次输入：**MYSQL_HOME**
**C:\Program Files\MySQL\MySQL Server 8.0\bin**，点击确定即可。
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521231355.png)
同样的在上面用户变量执行相同的操作
然后去到终端输入：**mysql -u root -p**验证
![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220521232855.png)

### 欧克，到这里 MySQL 安装就完成了

---

本次参考网站：
MySQL 安装：[mysql](https://blog.csdn.net/qq_40907977/article/details/109645908)
环境变量配置：

---

## [1](https://blog.csdn.net/fwdwqdwq/article/details/124006440)

## [2](https://blog.csdn.net/weixin_43914658/article/details/109764696)

[3](https://blog.csdn.net/weixin_43914658/article/details/111086077?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-111086077-blog-114580551.pc_relevant_antiscanv2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-111086077-blog-114580551.pc_relevant_antiscanv2&utm_relevant_index=1)

---

作者：**灰仙杯-小智**
联系作者：[github](https://github.com/dengzhu-hub/for_picgo)
[博客](https://jackdeng.top/)
