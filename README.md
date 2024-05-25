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