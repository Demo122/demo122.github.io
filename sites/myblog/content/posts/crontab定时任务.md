---
title: "crontab定时任务"
date: 2022-06-09T13:52:20+08:00
draft: false
categories: ["linux"]
tags: ["crontab定时任务"]
---
## crontab定时任务

**Linux crontab 是用来定期执行程序的命令。**

**当安装完成操作系统之后，默认便会启动此任务调度命令。**

**crond 命令每分钟会定期检查是否有要执行的工作，如果有要执行的工作便会自动执行该工作。**

### 安装使用crontab

- 安装

  ```shell
  sudo apt-te install cron
  ```

- 使用

    ```shell
    service crond start    //启动服务
    service crond stop     //关闭服务
    service crond restart  //重启服务
    service crond reload   //重新载入配置
    service crond status   //查看服务状态
    ```

- 常用命令
  - 查看crontab定时执行任务列表   `crontab -l`
  - 编辑crontab定时执行任务 `crontab -e`
  - 删除crontab定时任务 `crontab -r`

### 编写crontab -e

- nano使用

  **```ctrl+o```  后，filename.....，直接回车保存文件**

  **```ctrl+x``` 退出**

- 官方例子

```shell
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command

```

**时间格式如下：**

> ```f1 f2 f3 f4 f5 program```

- 其中 f1 是表示分钟，f2 表示小时，f3 表示一个月份中的第几日，f4 表示月份，f5 表示一个星期中的第几天。program 表示要执行的程序。
- 当 f1 为 * 时表示每分钟都要执行 program，f2 为 * 时表示每小时都要执行程序，其馀类推
- 当 f1 为 a-b 时表示从第 a 分钟到第 b 分钟这段时间内要执行，f2 为 a-b 时表示从第 a 到第 b 小时都要执行，其馀类推
- 当 f1 为 */n 时表示每 n 分钟个时间间隔执行一次，f2 为 */n 表示每 n 小时个时间间隔执行一次，其馀类推
- 当 f1 为 a, b, c,... 时表示第 a, b, c,... 分钟要执行，f2 为 a, b, c,... 时表示第 a, b, c...个小时要执行，其馀类推

> ```
> *    *    *    *    *
> -    -    -    -    -
> |    |    |    |    |
> |    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
> |    |    |    +---------- 月份 (1 - 12) 
> |    |    +--------------- 一个月中的第几天 (1 - 31)
> |    +-------------------- 小时 (0 - 23)
> +------------------------- 分钟 (0 - 59)
> ```

- 实例
  - **每一分钟执行一次 /bin/ls：** ```* * * * * /bin/ls```

### 运行python程序

- 运行python程序需要注意要使用绝对路径，包括解释器和py文件的绝对路径

  可以通过```whereis python```来查看python解释器的位置

- 例子

  ```shell
  */58 * * * * /usr/bin/python3 /home/xx/pycode/jcad.py >> /home/xx/pycode/jcad.log
  ```

  

