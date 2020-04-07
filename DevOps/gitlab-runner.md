##下载安装gitlab-runner最新版安装

```$xslt
wget -O /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
```

```$xslt
chmod +x /usr/local/bin/gitlab-runner
```

```$xslt
useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
```

```$xslt
gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
gitlab-runner start
```

##安装git 2.9.0
###先安装这个包，不然git命令报git fatal: Unable to find remote helper for 'https'

```$xslt
yum install curl-devel
```

```$xslt
wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.9.0.tar.gz

tar -zxvf git-2.9.0.tar.gz
cd git-2.9.0
./configure --prefix=/usr/local
make && make install
```

###环境变量
```$xslt
export PATH=$PATH:/usr/local/git/bin
export PATH=$PATH:/usr/local/git/libexec/git-core
```


##安装openjdk8
```$xslt
yum -y install java-1.8.0-openjdk java-1.8.0-openjdk-devel
```
  
###环境变量
```$xslt
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el7_7.x86_64
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
```

##安装maven
```$xslt
wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
```
###环境变量
```$xslt
MAVEN_HOME=/usr/local/maven
export PATH=${MAVEN_HOME}/bin:${PATH}
```
