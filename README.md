**前言：**在之前的文章[批量执行crontab指定条目的注释和解注释](https://blog.51cto.com/3241766/2530255)提到过spug平台，本文具体的对该平台做详细介绍。

`Spug` 面向中小型企业设计的轻量级无 Agent 的自动化运维平台，整合了主机管理、主机批量执行、主机在线终端、文件在线上传下载、应用发布部署、在线任务计划、配置中心、监控、报警等一系列功能。

**环境说明：**

| 主机名  |  操作系统版本   |    ip地址    | docker版本 | Spug版本 | 备注       |
| :-----: | :-------------: | :----------: | ---------- | :------: | ---------- |
| ansible | Centos 7.6.1810 | 172.27.34.51 | 18.09.9    |  v2.3.8  | 管理服务器 |

## 一、特性

- **批量执行:** 主机命令在线批量执行
- **在线终端:** 主机支持浏览器在线终端登录
- **文件管理:** 主机文件在线上传下载
- **任务计划:** 灵活的在线任务计划
- **发布部署:** 支持自定义发布部署流程
- **配置中心:** 支持 KV、文本、json 等格式的配置
- **监控中心:** 支持站点、端口、进程、自定义等监控
- **报警中心:** 支持短信、邮件、钉钉、微信等报警方式
- **优雅美观:** 基于 Ant Design 的 UI 界面
- **开源免费:** 前后端代码完全开源

本文会对‘批量执行’、‘在线终端’、‘文件管理’、‘任务计划’、‘监控中心’、‘报警中心’等功能。‘应用发布’和‘配置中心’下篇再做介绍。

## 二、软件安装

### 1.安装软件

```bash
[root@ansible yaml]# docker pull registry.aliyuncs.com/openspug/spug
[root@ansible yaml]# docker run -d --name=spug -p 80:80 -v /mydata/:/data registry.aliyuncs.com/openspug/spug
[root@ansible yaml]#  docker exec spug init_spug admin spug.dev
```

![image-20200821172347258](https://i.loli.net/2020/09/24/wl7CtxUh4sHR3MV.png)

通过docker方式安装，docker安装请参考：[k8s实践(一)：Centos7.6部署k8s(v1.14.2)集群](https://blog.51cto.com/3241766/2405624)中docker安装章节。

'-p 80:80'，spug的80端口映射端口为系统的80端口，‘-v /mydata/:/data’将容器的/data路径映射为系统的/mydata目录。

初始化管理员账号admin，密码为spug.dev

### 2.登陆

[http://172.27.34.51](http://172.27.34.51/)

![image-20200918154615369](https://i.loli.net/2020/09/24/uQy72t4AiR5ZsCT.png)

## 三、工作台

![image-20200918161814176](https://i.loli.net/2020/09/24/sTK8YnArMbFi7w3.png)

工作台是一个概览，包含应用、主机任务、监控和报警等信息。

## 四、主机管理

### 1.新建主机

![image-20200918162405102](https://i.loli.net/2020/09/24/26gFxdAzJ5j7hHO.png)

'主机类别'为自定义类别，分别输入主机名和连接地址，单击验证

![image-20200918162427616](https://i.loli.net/2020/09/24/G2JSr7cNjMFekD5.png)

输入root用户密码

![image-20200918162451768](https://i.loli.net/2020/09/24/G2JSr7cNjMFekD5.png)

主机添加成功

### 2.批量导入主机

![image-20200918162651704](https://i.loli.net/2020/09/24/ITZzNetupjUs5Wy.png)

使用批量导入方式导入主机，可以先下载模板在更新上传。

![image-20200918163520497](https://i.loli.net/2020/09/24/29blJ1Bm56Xzstc.png)

登陆密码一栏填写个主机密码，若为空则为默认密码'spug-default-keys'

![image-20200918163740084](https://i.loli.net/2020/09/24/GJqyDZKLF65gf9Q.png)

导入成功

![image-20200927095138906](https://i.loli.net/2020/09/27/lSu7Yfhmp8iyAV6.png)

登陆密码为传输公钥时使用，系统不保存密码，下次可免密登陆访问。

通信原理： 第一次在登录的时候会生产公私钥，密码只是用在第一次发送公钥上。

![image-20200918164015155](https://i.loli.net/2020/09/24/jN9quHy1CwIKamt.png)

公钥保存在各个被管理主机上，私钥存在于spug平台里(不是172.27.34.51服务里上面)。

## 五、在线终端

![image-20200918164208432](https://i.loli.net/2020/09/24/FXgJV6GKvqPUwya.png)

单机主机栏后面的'Console'即可**免密**进入终端

![image-20200918164347905](https://i.loli.net/2020/09/24/RJuql9P64KCvcO1.png)

进入终端，执行'df -h'命令

## 六、文件管理

进入在线终端后点击右上角的'文件管理器'即可进行文件上传下载操作

![image-20200918164609045](https://i.loli.net/2020/09/24/54CWupqnb98rAmU.png)

![image-20200918164551678](https://i.loli.net/2020/09/24/OUJX6QESuHZBlIj.png)

spug可方便的进行文件上传下载，不用使用ftp工具或者繁琐的命令。

## 七、批量执行

该功能为spug核心功能之一，现从命令方式和模板方式演示。

### 1.命令方式

![image-20200918165006138](https://i.loli.net/2020/09/24/HZWeCzxl5jw1LMP.png)

选择执行主机ansible、test162、test163，执行命令'df -h'和'echo "hello world"'

**执行结果：**

![image-20200918165122193](https://i.loli.net/2020/09/24/PGgWFhxHQDCuvbf.png)

### 2.模板方式

![image-20200918165248650](https://i.loli.net/2020/09/24/tXBYLDuFApCOUkx.png)

新建两个模板'注释crontab'和'解注释crontab'，模板内容其实也是shell脚本，比如'注释crontab'：

![image-20200918165410058](https://i.loli.net/2020/09/24/6qk3WOUjGp9f1Bu.png)

**模板方式批量执行：**

![image-20200918165511649](https://i.loli.net/2020/09/24/1wiVL5Ehfz2Aye9.png)

执行完成，该模板内容为批量注释指定的crontab

![image-20200918165557753](https://i.loli.net/2020/09/24/RqzBYFvwresVWQI.png)

## 八、任务计划

![image-20200918171349009](https://i.loli.net/2020/09/24/qE3afDp1dzscgJR.png)

新建任务计划date，任务类型可自定义，失败通知选钉钉，后面会有介绍，下一步

![image-20200924153432103](https://i.loli.net/2020/09/24/M2IiPnYyCtmjubG.png)执行对象选择test162和test163

![image-20200918171615712](https://i.loli.net/2020/09/24/YRIN8yL96F7wsv1.png)

选择执行规则，UNIX Cron和linux的crontab类似，这里设置每分钟执行一次。

![image-20200918171711242](https://i.loli.net/2020/09/24/E2ASjKqYNxIc7m4.png)

激活任务

![image-20200918172104322](https://i.loli.net/2020/09/24/fncPitGhNAZYguQ.png)

验证：

![image-20200918172427399](https://i.loli.net/2020/09/24/nZoSpfJGI3yC8lH.png)

每分钟向/tmp/date.txt文件输入当前时间。

## 九、报警中心	

在介绍监控中心之前先介绍报警中心

### 1.报警历史

![image-20200921100329614](https://i.loli.net/2020/09/24/cEeauNYyASf2mFl.png)

报警历史可以查看报警的历史信息，包括任务名、通知方式、通知对象和发生时间等。

### 2.报警联系人

以添加联系人loong576说明

#### 2.1 报警联系人概览

![image-20200921101210081](https://i.loli.net/2020/09/24/6gwcYaT9b5tGqNK.png)

告警方式包括邮箱、微信、钉钉和企业微信。

#### 2.2 获取微信Token

关注微信公众号'Spug运维'，点击'我的'菜单获取

![image-20200921101607742](https://i.loli.net/2020/09/24/sFKxlkjh8UvrQWV.png)

#### 2.3 获取钉钉webhook

![image-20200921102309212](https://i.loli.net/2020/09/24/tR1NY9JGWInDgr2.png)

首先新建群聊

![image-20200921165532908](https://i.loli.net/2020/09/24/rTtDNByMp1adCv7.png)

选择接收的联系人，创建群‘spug告警接收’

![image-20200921165827672](https://i.loli.net/2020/09/24/Wr7snvoRx5AGOpT.png)

![image-20200921165939988](https://i.loli.net/2020/09/24/7ErR3iMmZvw8qal.png)

![image-20200921170026675](https://i.loli.net/2020/09/24/yKis48zncETtWVF.png)

![image-20200921170141108](https://i.loli.net/2020/09/24/L5mdjoh6v9isWJT.png)

![image-20200921170119747](https://i.loli.net/2020/09/24/WEA3lXfLoGyDNvk.png)

![image-20200921170218316](https://i.loli.net/2020/09/24/zgM3LTqIixGKb9U.png)

点击群聊窗口右边的‘群设置’，‘智能群助手’，‘添加机器人’，‘自定义’，单击‘添加’

![image-20200921170449444](https://i.loli.net/2020/09/24/kEtMlIY9GDcadX1.png)

根据实际情况填写安全设置，我这里填的是‘自定义关键词’，最多匹配10个，任意一个关键词被匹配到就会接收消息。

![image-20200921170625946](https://i.loli.net/2020/09/24/a4RmMQnWNXwclE9.png)

完成机器人添加，复制webhook。

#### 2.4 获取企业微信webhook

企业微信获取webhook方式和钉钉有些类似，也是先建群，然后添加机器人。

![image-20200923154249811](https://i.loli.net/2020/09/24/3iuD4xolh9eKFTH.png)

建群，选中群，添加群机器人



![image-20200923154259574](https://i.loli.net/2020/09/24/WOkz12Y7yStcfp8.png)

![image-20200923154336795](https://i.loli.net/2020/09/24/pdQnvNGrztsg38Y.png)

创建一个机器人

![image-20200923154414611](https://i.loli.net/2020/09/24/5egkB36bV8lvCOF.png)

![image-20200923154445961](https://i.loli.net/2020/09/24/bE6xhkGT2JmXjLt.png)

复制webhook地址

### 3.报警联系人组

![image-20200923154828360](https://i.loli.net/2020/09/24/48Z1OplSkDevQx9.png)

告警是以组的方式发送的，新建告警组test_team，将告警联系人loong576加入改组。

![image-20200923154924773](https://i.loli.net/2020/09/24/tXeLxAzHSEC3lbf.png)

## 十、监控中心

### 1.监控中心概览

![image-20200923155021034](https://i.loli.net/2020/09/24/jt2B6RPSi7eTw53.png)

监控方式有四种：站点监控、端口监控、站点监控和自定义监控。这里以端口监控和自定义监控做说明。

### 2.端口监控

![image-20200923160107117](https://i.loli.net/2020/09/24/eEOvnJcBQsf62ZN.png)

新建端口监控，监控地址为172.27.34.51，监控端口为8808

![image-20200923160232920](https://i.loli.net/2020/09/24/GOf3tIULqpZQDYK.png)

监控频率为1分钟，即1分钟检查一次；报警阀值为3次，即检查3次不成功才发出报警；报警联系人组为test_team；报警方式为微信、钉钉、邮件和企业微信；通道沉默为5分钟，表示每5分钟发送一次报警消息。

![image-20200923160438695](https://i.loli.net/2020/09/24/eGqn7y3tmkfjVLY.png)

提交后等待检测

![image-20200923160751763](https://i.loli.net/2020/09/24/lpKfmk4vcSobCG3.png)

发现8808端口检测异常

#### 2.1 微信告警

![image-20200923161204956](https://i.loli.net/2020/09/24/TNIwmqQChiV7Eua.png)

#### 2.2 钉钉告警

![image-20200923161300810](https://i.loli.net/2020/09/24/IONmF2Un7rcKGly.png)

#### 2.3 邮件告警

![image-20200923161331167](https://i.loli.net/2020/09/24/Mwyzko6dNJ4UYOn.png)

![image-20200923161340884](https://i.loli.net/2020/09/24/Z9xtaOYLr7VFEzo.png)

#### 2.4 企业微信告警

![image-20200923161411558](https://i.loli.net/2020/09/24/LC8MdY3hZ2x4Ees.png)

### 3. 自定义监控

以监控文件系统使用率为例，超过5%即报警，监控脚本如下：

```bash
#!/bin/bash
num=5
df -h|grep -vE 'tmpfs|cdrom'| awk -F '[ %]+' 'NR == 1 {next} {print $6 "     "  $5}' |while read df_file;
do 
  value=$(echo $df_file | awk '{ print $2}')
  name=$(echo $df_file | awk '{ print $1 }')
  if [ $value -ge $num ]
  then
  echo "主机`hostname`文件系统 $name 使用率为 $value% "
  fi
done

for i in $(df -h| awk -F '[ %]+' 'NR == 1 {next} {print $5}'|xargs) 
do 
  if [ $i -ge $num ]
  then
  exit $i 
  fi
done
```

![image-20200923162442880](https://i.loli.net/2020/09/24/YdnVR4m2AxUlvGF.png)

脚本逻辑：首先设置阀值为'num=5'，通过'df -h'获取文件系统使用率所在的列，然后与阀值循环比较，如果大于阀值则输出告警信息'主机`hostname`文件系统 $name 使用率为 $value% '。

自定义告警的原理：通过脚本判断监控项，脚本执行退出状态码为 0 则判定为正常，其他为异常。

#### 3.1 报警信息

**微信：**

![image-20200923162416094](https://i.loli.net/2020/09/24/xeMPYRignmB1KSc.png)

**钉钉：**

![image-20200923162503570](https://i.loli.net/2020/09/24/S9IcjJQKzUmRoV1.png)

**邮件：**

![image-20200923162527846](https://i.loli.net/2020/09/24/8gUH41aZRATKeV2.png)

**企业微信：**

![image-20200923162548995](https://i.loli.net/2020/09/24/5Ac1gsXklvxV3MQ.png)

## 十一、系统管理

### 1.角色管理

![image-20200923163513658](https://i.loli.net/2020/09/24/muezsvBOFaYoQ43.png)

新建角色test_role

![image-20200923163613674](https://i.loli.net/2020/09/24/oXSxd27W6uGwQlR.png)

分配权限如图

### 2.账户管理

![image-20200923163715896](https://i.loli.net/2020/09/24/9AixnXJMoIbaN8p.png)

新建账户loong576，分配角色test_role

![image-20200927093659364](https://i.loli.net/2020/09/27/Aca2p4lNf1dtqLg.png)

#### 2.1 账户禁用问题

**现象：**

![image-20200917101915977](https://i.loli.net/2020/09/24/imnENqsJ6hOeClF.png)

**解决：**

```bash
[root@ansible ~]# docker exec spug python3 /data/spug/spug_api/manage.py user enable -u admin
```

![image-20200917101612841](https://i.loli.net/2020/09/24/yGxX7bWk16paFjw.png)



#### 2.2 重置密码

**现象：**

![image-20200917101440925](https://i.loli.net/2020/09/24/HV2TALECQGnKzoZ.png)

**解决：**

```bash
[root@ansible ~]# docker exec spug python3 /data/spug/spug_api/manage.py user reset -u admin -p Admin01!
```

![image-20200917101459190](https://i.loli.net/2020/09/24/CZldevuJqSYRyFD.png)

### 3.系统设置

#### 3.1 秘钥设置

![image-20200923163915578](https://i.loli.net/2020/09/24/7gz68qC91JGmjbW.png)

spug 有自己的密钥对,公钥保存在被管理的主机内，私钥保存在spug平台内(不是管理主机172.27.34.51里)。通过

## 十二、其它问题

### 1.root无法直接登录问题

#### 1.1 问题说明

由于root用户禁止直接登录，新建主机时登录用户不能设置为root，否则会报错，此时如果需要执行需要root权限的命令时，需要加sudo，但是运行sudo时需要输入密码确认，spug平台批量执行时没有交互窗口，运行命令会报错，此时则需要进行提权操作且免密。

```bash
[monitor@work01 /]$ id
uid=1002(monitor) gid=1002(monitor) 组=1002(monitor)
[monitor@work01 /]$ more /etc/sudoers
/etc/sudoers: 权限不够
[monitor@work01 /]$ sudo more /etc/sudoers|grep monitor
monitor    ALL=(ALL)   NOPASSWD:    ALL
```

修改文件'/etc/sudoers'，新增：'monitor    ALL=(ALL)   NOPASSWD:    ALL'

#### 1.2 monitor用户获取root权限运行示例

**不使用sudo情况：**

![image-20200826155136289](https://i.loli.net/2020/09/24/kBMeA1jmGDXxEFS.png)

**使用sudo：**

![image-20200826155158912](https://i.loli.net/2020/09/24/7FuinbwjQNJCrtX.png)

![image-20200826155221773](https://i.loli.net/2020/09/24/eHYILDZngG153td.png)

使用sudo运行需要root权限的额命令，直接运行，不需要二次输入密码。

### 2.打通网络

如果是生产环境无法联网的话，发送告警信息则需要打通网络

```bash
到微信：http://spug-wx.qbangmang.com 80
到钉钉：https://oapi.dingtalk.com 443
到企业微信：https://qyapi.weixin.qq.com 443
```

### 3.设置容器自启动

```bash
[root@ansible ~]# docker update --restart=always spug
spug
```

设置后若主机重启容器spug会自动启动，无需手动在拉起来

### 4.更换平台ip

如需更换ip，则直接修改，然后重启主机即可。

## 十三、总结

轻量、快捷、好用是spug的特点，部署简单，安全可靠、无agent、可视化，可以快速高效的批量对主机进行命令分发、监控等，非常适用于日常变更上线操作。spug平台既可当跳板机，也可以替代堡垒机部分功能，如进入console、文件上传下载等。

## 参考

官网：https://www.spug.dev/

github：https://github.com/openspug/spug

文档：https://www.spug.dev/docs/about-spug

&nbsp;
&nbsp;
&nbsp;

&nbsp;

&nbsp;

![image-20200309002139092](https://i.loli.net/2020/03/09/al2VNxU5OwoE4dr.png)
