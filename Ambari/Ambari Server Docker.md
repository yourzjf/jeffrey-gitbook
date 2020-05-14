# Ambari Server Docker 安装

Ambari 版本：2.7.4

依赖 JDK8、MySQL

兼容矩阵：https://supportmatrix.hortonworks.com/

​	HDP : 3.0.0 ~ 3.1.4

​	JDK：1.8.0_77+

​    MySQL 5.7 InnoDB



Docker 创建实例命令：

​    docker run -itd --name ambari -m 10240M -p 8080:8080 -v /f:/software centos:centos7



在线安装：

​    yum install -y wget

​    wget -O /etc/yum.repos.d/ambari.repo http://public-repo-1.hortonworks.com/ambari/centos7/2.x/updates/2.7.4.0/ambari.repo

​    yum install -y ambari-server



可供参考

https://blog.csdn.net/weixin_42348946/article/details/90268875

本地压缩包安装： https://blog.csdn.net/liuyunshengsir/article/details/102885122

制作docker镜像： https://www.freesion.com/article/490555209/

