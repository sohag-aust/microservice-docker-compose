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

    Account Microservice :: http://172.18.0.11:8072/eazybank/accounts/api/contact-info  [hit again and again, though it is showing 504 from chrome, look at the log, account microservice fallback method is triggered]
    Card Microservice :: using apache benchmark command :: ab -n 10 -c 2 -v 3 http://localhost:8072/eazybank/cards/api/contact-info 