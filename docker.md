### docker 安装组件 命令整理


#### nacos 运行

> docker run -d 
> -e PREFER_HOST_MODE=hostname 
> -e MODE=cluster 
> -e NACOS_APPLICATION_PORT=8846 
> -e NACOS_SERVERS="172.26.209.218:8846 172.26.209.218:8847" 
> -e SPRING_DATASOURCE_PLATFORM=mysql 
> -e MYSQL_SERVICE_HOST=172.26.209.218 
> -e MYSQL_SERVICE_PORT=3306 
> -e MYSQL_SERVICE_USER=root 
> -e MYSQL_SERVICE_PASSWORD=root 
> -e MYSQL_SERVICE_DB_NAME=nacos 
> -e NACOS_SERVER_IP=172.26.209.218 
> -e JVM_XMS=256m 
> -e JVM_XMX=256m 
> -e JVM_XMN=256m 
> -p 8846:8846 
> --name nacos8846 
> nacos/nacos-server:1.3.0

#### prometheus 运行
> docker run  -d -p 9090:9090 
> -v /opt/docker/prometheus/server/prometheus.yml:/etc/prometheus/prometheus.yml 
> --name prometheus prom/prometheus

#### mysqld-exporter 运行
> docker run -d -p 9104:9104 -e DATA_SOURCE_NAME=”exporter:123456@(172.26.209.218:3306)/” prom/mysqld-exporter

#### jenkins 运行
> docker run -d -u 0 
> --privileged --name jenkins 
> -p 20000:8080 
> -v /opt/docker/jenkins_home/:/var/jenkins_home 
> -v /opt/software/apache-maven-3.8.1/:/maven/apache-maven-3.8.1 
> -v /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.275.b01-1.el8_3.x86_64/:/jdk/jdk.1.8.0.261 
> jenkins/jenkins
> 
> -u 0 指的是传入root账号ID，覆盖容器中内置的账号
> -v /opt/docker/jenkins_home/:/var/jenkins_home指的是 将docker容器内的目录/var/jenkins_home映射到宿主机/opt/docker/jenkins_home目录上
> --privileged 赋予最高权限
> 
#### elasticsearch集群 运行

> 1. docker run --name elasticsearch-node1 -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -v /opt/docker/es/conf/elasticsearch-1.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /opt/docker/es/data:/usr/share/elasticsearch/data1 -v /opt/docker/es/plugins:/usr/share/elasticsearch/plugins -d elasticsearch:7.12.1
> 2. docker run --name elasticsearch-node2 -p 9201:9201 -p 9301:9301 -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -v /opt/docker/es/conf/elasticsearch-2.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /opt/docker/es/data:/usr/share/elasticsearch/data2 -v /opt/docker/es/plugins:/usr/share/elasticsearch/plugins -d elasticsearch:7.12.1
> 3. docker run --name elasticsearch-node3 -p 9202:9202 -p 9302:9302 -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -v /opt/docker/es/conf/elasticsearch-3.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /opt/docker/es/data:/usr/share/elasticsearch/data3 -v /opt/docker/es/plugins:/usr/share/elasticsearch/plugins -d elasticsearch:7.12.1
> 
> 注意 
> 
> 其中elasticsearch.yml是挂载的配置文件，data是挂载的数据，plugins是es的插件，如ik，而数据挂载需要权限，需要设置data文件的权限为可读可写,需要下边的指令。
> chmod -R 777 要修改的路径
> -e "discovery.type=single-node" 设置为单节点
> 
> 特别注意：
> 
> -e ES_JAVA_OPTS="-Xms256m -Xmx256m" \ 测试环境下，设置ES的初始内存和最大内存，否则导致过大启动不了ES


#### kibana 运行
> docker run -p 5601:5601 -d --name kibana -v /opt/docker/kibana/conf/kibana.yml:/usr/share/kibana/config/kibana.yml --privileged=true kibana:7.12.1



#### 项目 运行
> cd /projects
>
> cd leo-job-admin/
> 
> mvn clean
> 
> mvn -Dtest package
> 
> docker build  -t leo/leo-job-admin:v1 .
> 
> docker run -d -p 1000:1000 --name leo-job-admin leo/leo-job-admin:v1
> 
> docker build  -t leo/job-executor:v1 .
> 
> docker run -d -p 1002:1002 --name job-executor leo/job-executor:v1
> 
> docker build  -t leo/spider-data:v1 .
> 
> docker run -d -p 10001:10001 --name spider-data leo/spider-data:v1


