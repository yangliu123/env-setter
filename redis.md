#Redis开发环境搭建
sudo apt-get install python-software-properties software-properties-common

sudo add-apt-repository ppa:chris-lea/redis-server

sudo apt-get update
sudo apt-get install redis-server

sudo vi /etc/redis/redis.conf
# bind 127.0.0.1

redis-cli
> dbsize
> keys *
> type <key>
> del <key>
> flushall

> hgetall <key>

>llen <key>
>lrange <key> 0 -1
