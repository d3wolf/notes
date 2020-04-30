wget http://pkg.jenkins-ci.org/redhat-stable/jenkins-2.7.3-1.1.noarch.rpm
 rpm -ivh jenkins-2.7.3-1.1.noarch.rpm
 
 
  配置jenkis的端口
 
 vim /etc/sysconfig/jenkins
 
 找到修改端口号：
 
 JENKINS_PORT="8180"  此端口不冲突可以不修改，默认是8080



（1）/usr/lib/jenkins/：jenkins安装目录，WAR包会放在这里。
（2）/etc/sysconfig/jenkins：jenkins配置文件，“端口”，“JENKINS_HOME”等都可以在这里配置。 
（3）/var/lib/jenkins/：默认的JENKINS_HOME。 
（4）/var/log/jenkins/jenkins.log：Jenkins日志文件




首次进入会要求输入初始密码如下图， 
初始密码在：cat /var/lib/jenkins/secrets/initialAdminPassword




    卸载
    rpm -e jenkins #rpm卸载
    rpm -ql jenkins #检查是否卸载成功
    find / -iname jenkins | xargs -n 1000 rm -rf 彻底删除残留文件




以centOS系统为例，记录下修改Jenkins以root用户运行的方法。
修改Jenkins配置文件

# 打开配置文件
vim /etc/sysconfig/jenkins
# 修改$JENKINS_USER，并去掉当前行注释
$JENKINS_USER="root"

    1
    2
    3
    4

修改Jenkins相关文件夹用户权限

chown -R root:root /var/lib/jenkins
chown -R root:root /var/cache/jenkins
chown -R root:root /var/log/jenkins

    1
    2
    3

重启Jenkins服务并检查运行Jenkins的用户是否已经切换为root

# 重启Jenkins（若是其他方式安装的jenkins则重启方式略不同）
service jenkins restart
# 查看Jenkins进程所属用户
ps -ef | grep jenkins
# 若显示为root用户，则表示修改完成



#为啥提示找不到命令

我对于java或maven的路径的环境变量是放在/etc/profile中的, 而/etc/profile只有在用户登录的时候才会被load,Jenkins在运行命令时,使用的是Non-login的方式,而这种方式在运行命令时,/etc/profile是不会被load进来的,所以jenkins只能在当前路径下寻找可执行文件
