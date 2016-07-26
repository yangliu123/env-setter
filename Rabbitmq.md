#Rabbitmq开发环境搭建
sudo bash -c 'echo "deb http://www.rabbitmq.com/debian/ testing main" > /etc/apt/sources.list.d/rabbitmq.list'
wget -q http://www.rabbitmq.com/rabbitmq-signing-key-public.asc -O- | sudo apt-key add -

sudo apt-get update
sudo apt-get install rabbitmq-server

sudo service rabbitmq-server start
sudo rabbitmqctl status
sudo rabbitmqctl help

sudo rabbitmqctl add_user <username> <password>
sudo rabbitmqctl set_user_tags <username> administrator
sudo rabbitmqctl list_users

sudo rabbitmqctl add_vhost /<vhost_name>
sudo rabbitmqctl set_permissions -p /<vhost_name> <username> ".*" ".*" ".*" 

sudo rabbitmqctl list_vhosts
sudo rabbitmqctl list_user_permissions <username>
sudo rabbitmqctl list_queues -p /<vhost_name>
sudo rabbitmqctl list_bindings -p /<vhost_name>

sudo rabbitmq-plugins list
sudo rabbitmq-plugins enable rabbitmq_management_visualiser
sudo service rabbitmq-server restart

-------------------------------------------------------------------------------
rabbitMQ集群部署
http://www.rabbitmq.com/clustering.html
# 各台机器下载安装rabbitMQ
# 各台机器设置统一的cookie值 文件为：/var/lib/rabbitmq/.erlang.cookie
# 各台机器启动队列 sudo rabbitmq-server -detached
# 各台机器查看自己的cluster状态 sudo rabbitmqctl cluster_status
# 各台机器查看自己的版本号是否一致sudo rabbitmqctl status
# 停掉机器2的队列，加入机器1的集群
## sudo rabbitmqctl stop_app
## sudo rabbitmqctl join_cluster --ram rabbit@rabbit1
## sudo rabbitmqctl start_app
# 同上停掉机器3的队列，加入集群
## sudo rabbitmqctl stop_app
## sudo rabbitmqctl join_cluster --ram rabbit@rabbit1
## sudo rabbitmqctl start_app
# 各台机器查看整个cluster状态 sudo rabbitmqctl cluster_status
# spring配置
http://docs.spring.io/spring-amqp/reference/html/amqp.html#d4e209
# 集群主动将一个出问题的节点移出集群
## cluster$ sudo rabbitmqctl cluster_status 查看节点运行情况
## cluster$ sudo rabbitmqctl forget_cluster_node rabbit@allen 移除该节点
## cluster$ sudo rabbitmqctl cluster_status
## allen$ sudo rabbitmqctl stop_app 节点自身重置成单节点
## allen$ sudo rabbitmqctl reset
## allen$ sudo rabbitmqctl start_app
## allen$ sudo rabbitmqctl cluster_status
# 如果一个节点启动不了，无法通过stop_app, reset来重置，则可以通过删除/var/lib/rabbmq下的内容来重置，然后重启节点
