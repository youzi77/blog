## docker -nginx
docker run --name nginx -p 80:80 \
-v /home/docker/nginx/html:/usr/share/nginx/html \
-v /home/docker/nginx/log:/var/log/nginx \
-v /home/docker/nginx/nginx.conf:/etc/nginx/nginx.conf:ro \
-v /home/docker/nginx/conf.d:/etc/nginx/conf.d \
--restart always \
--privileged=true -d nginx
- 重启：docker exec -i nginx nginx -s reload
## doker -mysql
docker run -d \
    --name mysql \
    --restart=always \
    -p 13306:3306 \
    -v /etc/localtime:/etc/localtime \
    -v /home/docker/mysql/conf:/etc/mysql/conf.d \
    -v /home/docker/mysql/data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=linewell@2020 \
    mysql:5.7 \
    --lower_case_table_names=1



## docker 进入mysql

```ruby
docker exec -it 89c5b9c81e74  bash

```