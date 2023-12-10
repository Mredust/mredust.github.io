# 备份数据库



## 1.编写shell脚本

```sh
#!/bin/bash
#备份路径
BACKUP=/data/backup/db
#备份日期
DATETIME=$(date +%Y-%m-%d_%H%M%s)

#数据库地址
HOST=localhost
#数据库用户名
DB_USER=root
#数据库密码
DB_PW=motianze4
#备份的数据库
DATABASE=test

#判断目录是否存在
[ ! -d "${BACKUP}/${DATETIME}" ] && mkdir -p "${BACKUP}/${DATETIME}"

#备份数据库
mysqldump -u ${DB_USER} -p ${DB_PW} --host=${HOST} -q -R --database=${DATABASE} | gzip > ${BACKUP}/${DATETIME}/$DATETIME.sql.gz

#将文件处理成tar.gz
cd ${BACKUP}
tar -zcvf $DATETIME.tar.gz ${DATETIME}

#删除gz
rm -rf ${BACKUP}/${DATETIME}

#删除10天前的数据库
find ${BACKUP} -atime +10 -name "*.tar.gz" -exec rm -rf {} \;

echo "数据库${DATABASE}备份成功"
```



## 2.进行任务调度

```shell
crontab -e

#每天2点30分备份一次
30 2 * * *  脚本存放的位置
```

