# ROTTEN POTATOES

O Rotten Potatoes é uma aplicação escrita em Python com o framework Flask que utiliza um banco MongoDb.Essa aplicação faz parte dos desafios do curso Kubedev.

Para criar a estrutura da nossa aplicação em containers para uso em ambiente de desenvolvimento temos um arquivo docker-compose com o que será necessário para rodar a nossa aplicação.

## DOCKER COMPOSE

No nosso arquivo docker compose, vamos iniciar informando a rede a ser criada para estabelecer a comunicação entre os containers da nossa solução.

```docker
networks:
  rotten-potatoes-net:
    driver: bridge
```
Para disponibilizar os containers necessários a nossa aplicação vamos criar dois serviços:

- rotten-potatoes.
- mongo_db

```docker
services:
  rotten-potatoes:
    image: norbertosantos/rotten-potatoes:v1
    ports:
      - 5000:5000
    networks:
      - rotten-potatoes-net
    depends_on:
      - mongo_db
    environment:
      MONGODB_DB: admin
      MONGODB_HOST: mongo_db
      MONGODB_PORT: 27017
      MONGODB_USERNAME: mongouser
      MONGODB_PASSWORD: mongopwd
  
  mongo_db:
    image: mongo:4.4.3
    restart: always
    ports:
      - 27017:27017
    networks:
      - rotten-potatoes-net
    environment:
      MONGO_INITDB_ROOT_USERNAME: mongouser
      MONGO_INITDB_ROOT_PASSWORD: mongopwd
    volumes:
      - mongo_rotten_potatoes_vol:/data/db 

```
No serviço **rotten-potatoes** vamos usar uma imagem que está disponível no meu repositório no Docker Hub.

Para que possamos acessar a nossa aplicação no navegador vamos informar a porta 5000. Que é a porta utiliza por padrão em aplicações Flask.

No item networks vamos informar a rede **rotten-potatoes-net** a ser usada para a comunicação com o container do **mongo-db**.

Neste serviço também vamos informar que o serviço **rotten-potatoes** depende do serviço **mongo-db**.

A imagem do rotten-potatoes necessita que algumas variáveis de ambiente sejam informadas no item **environment**. São elas:

- **MONGODB_DB:** Nome do banco Mongo Db que será usado para armazenar os dados da aplicação.
- **MONGODB_HOST:** Nome do serviço que contem o banco Mongo Db(mongo-db).
- **MONGODB_PORT:** Porta a ser utilizada para conectar ao banco Mongo Db(27017).
- **MONGODB_USERNAME:** Usuário do banco Mongo Db.
- **MONGODB_PASSWORD:** Senha do banco Mongo Db.

No serviço **mongo_db** vamos utilizar a imagem oficial do mongodb na versão 4.4.3.

Para expor as conexões ao nosso container **mongo_db** vamos utilizar a porta 27017 e especificar no item ports.

Assim como no serviço rotten-potatoes, o serviço **mongo_db** também deve informar a rede **rotten-potatoes-net** para a comunicação com os containers da solução.

A imagem do mongodb também espera que algumas variáveis de ambiente sejam informadas. Estas serão informadas no item environments:

- **MONGO_INITDB_ROOT_USERNAME:** Usuário do banco de dados Mongo Db.
- **MONGO_INITDB_ROOT_PASSWORD:** Senha do banco de dados Mongo Db.

Para armazenar os dados dos filmes e avaliações do rotten-potatoes vamos criar um volume e associá-lo a pasta /data/db dentro do container do Mongo Db.

Dentro do nosso arquivo compose também precisamos especificar o volume que será usada no item volumes.

```docker
volumes: 
  mongo_rotten_potatoes_vol:
```
Para rodar o nosso compose. Vamos utilizar o seguinte comando:

```docker
docker-compose up
```
Para acessar a nossa aplicação do rotten-potatoes. Vamos digitar o endereço abaixo no navegador:

**http://localhost:5000**






