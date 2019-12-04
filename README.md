<h2># api-gateway-kong-</h2>

<h3>技术栈</h3>

    API GATEWAY : kong  
    Dashboard : konga   
    语言：golang   
    部署运行：docker   
    数据库：postgresql  

<h3>目标实现</h3>

    路由、jwt、oauth2、限流、熔断   

<h3>#docker安装postgresql</h3>

    1.参考：https://docs.konghq.com/install/docker/?_ga=2.187868897.876354488.1575430844-1130134141.1574840917
    2.pull 镜像：
        $ docker pull postgres:9.6
    3.run 容器：
        $ docker run -d --name kong-database \
                       --network=kong-net \
                       -p 5432:5432 \
                       -e "POSTGRES_USER=kong" \
                       -e "POSTGRES_DB=kong" \
                       postgres:9.6

<h3>#docker安装kong</h3>

    1.参考：https://docs.konghq.com/install/docker/?_ga=2.187868897.876354488.1575430844-1130134141.1574840917
    2创建一个Docker网络：
        $ docker network create kong-net
    3.pull 镜像：
        $ docker pull kong:latest
    4.run 容器：
        $ docker run -d --name kong \
             --network=kong-net \
             -e "KONG_DATABASE=postgres" \
             -e "KONG_PG_HOST=kong-database" \
             -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
             -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
             -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
             -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
             -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
             -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
             -p 8000:8000 \
             -p 8443:8443 \
             -p 8001:8001 \
             -p 8444:8444 \
             kong:latest
    5.测试：
        $ curl -i http://localhost:8001/
             
<h3>#docker安装konga</h3>
    
    1.参考：https://github.com/pantsel/konga
    2.pull 镜像：
        $ docker pull pantsel/konga
    3.run 容器：
        $ docker run -d -p 1337:1337 --network kong-net  --name konga -e "NODE_ENV=production"  pantsel/konga
    4.访问web：
        http://{ip}:1337