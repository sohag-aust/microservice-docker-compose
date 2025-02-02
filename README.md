### Docker compose files

    1. default : this folder maintains default configurations
    2. prod : this folder maintains prod configurations
    3. qa : this folder maintains qa configurations

    To run environment specific containers, go to specific folder first and run "docker-compose up" command

### MySql DB container from docker compose

    1. Firstly create new docker images for all services with tag s7
        like: configserver:s7, accounts:s7, loans:s7, cards:s7

    2. Then, chaange docker compose with mysql container related changes
    3. Remove all rabbitmq related things from docker-compose, as my services are not using the rabbitmq bus config related changes anymore


### Client side service discovery and load balancing for LOANS microservices
    
    1. create 2 instances of loans microservices, in the docker-compose file
    2. Up all the microservices using docker-compose up command
    3. After all the microservices is up check eureka server dashboard, there will be 2 loans microservices instances
    4. for testing client side service discovery and load balancing, first create accounts, cards, and loans first using APIS
    5. then hit /api/fetchCustomerDetails api from account microservices.
    6. Firstly it will give data, so, we don't understand properly how load balancing and service discovery is working for loans microservices
    7. Stop one container for loans microservices
    8. hit the /api/fetchCustomerDetails again, and again. one time it will give data and another time it will throw internal server exception
        because the api route to loans microservices which instance is down.
    9. So, we can understand the service discovery and load balancing from client side using the upper testing


# Section :: 10.6 :: Test RateLimiting with Gateway server for accounts and cards microservice

    firstly up docker-compose for default folder

    sudo docker-compose up

    if not succeeded then , run the docker-compose up command again
    for down : sudo docker-compose down

    After all container is up, then test services

    If container is still not up and running, then goto docker-compose and look at the left bar, for each service in the docker-compose
    there is a run button, so run the services as below order :
    
    ** configserver -> eureka-server -> redis -> accountdb -> cardsdb -> loansdb -> account-MS -> cards-MS -> loans-MS -> gatewayserver **

    Account Microservice :: http://172.18.0.11:8072/eazybank/accounts/api/contact-info  [hit again and again, though it is showing 504 from chrome, look at the log, account microservice fallback method is triggered]
    Card Microservice :: using apache benchmark command :: ab -n 10 -c 2 -v 3 http://localhost:8072/eazybank/cards/api/contact-info 

    View container logs using : docker logs -f <container-id>


# Section :: 11.1 :: Observability using Loki, and Alloy

    Docker Service running sequence

    ** configserver -> eureka-server -> redis -> accountdb -> cardsdb -> loansdb -> account-MS -> cards-MS -> loans-MS -> gatewayserver 
                        -> minio -> read -> write -> gateway -> backend -> alloy -> grafana


# Section :: 11.2 :: Observability using Actuator, Micrometer, Prometheus, Grafana

    Docker Service running sequence

    ** 
        -> configserver -> eureka-server -> redis -> accountdb -> cardsdb -> loansdb -> 
        -> account-MS -> cards-MS -> loans-MS -> gatewayserver ->
        -> minio -> read -> write -> gateway -> backend -> alloy -> grafana -> prometheus

    ** After Running all the Docker Containers, if we want to access prometheus, then try

            localhost:9090/targets

        here we will see all the running applications in the prometheus dashboard

        if we want to see CPU_USAGE of the applications, then type : system_cpu_usage in the query section of the dashboard and click execute,
        we can also see the output as a graph, by clicking graph section


    ** Grafana Dashboard For Prometheus Data **

    1. goto grafana dashboard page : https://grafana.com/grafana/dashboards/
    2. search any of the dashboard in the dashboard search section, ex : JVM micrometer,
    3. copy the link of the dashboard (https://grafana.com/grafana/dashboards/12271-jvm-micrometer/) 
    4. goto our grafana application in localhost:3000
    5. import the link to our grafana application. but before import,
    6. first we need to signin to grafana by username: admin , and password: admin
    7. goto dashboard section, 
    8. click new and select import section and import the link (https://grafana.com/grafana/dashboards/12271-jvm-micrometer/) 
    9. then load and after that select datasource to prometheus.
    10. we can add another dashboard like spring boot system monitor (https://grafana.com/grafana/dashboards/11378-justai-system-monitor/) by the same way

    ** Custom Dashboard **
    we can also set custom dashboard in the grafana dashboard, by clicking the New and New Dashboard section,
    and then click add section, where we will see visualization and row section
    the row section is specific to the specific microservice and visualization section is for adding graphs


    ** For Setting Alert use the following Site to generate custom webhook url **
    https://webhook.site/


# Section :: 11.3 :: Distributed Tracing using OpenTelemetry, Tempo, Grafana

    Docker Service running sequence

    ** 
        -> configserver -> eureka-server -> redis -> accountdb -> cardsdb -> loansdb -> 
        -> account-MS -> cards-MS -> loans-MS -> gatewayserver ->
        -> minio -> read -> write -> gateway -> backend -> alloy -> grafana -> prometheus -> tempo