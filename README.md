# mongodb-cluster
Comandos para a montagem de um cluster no MongoDB usando o docker.

### Criar rede

`docker network create mongoCluster` 

### Primeiro nó

`docker run -d --rm -p 27017:27017 --name mongo1 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo1` 

### Segundo nó

`docker run -d --rm -p 27018:27017 --name mongo2 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo2`

### Terceiro nó

`docker run -d --rm -p 27019:27017 --name mongo3 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo3`

### Quarto nó

`docker run -d --rm -p 27020:27017 --name mongo4 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo4`

### Configurar a inicialização do conjunto de réplicas

`docker exec -it mongo1 mongosh` 

`rs.initiate ({ _id: "myReplicaSet", members:[{_id:0, host: "mongo1"}, {_id:1, host: "mongo2"}, {_id:2, host: "mongo3"}, {_id:3, host: "mongo4"}]})` 

`exit` 

### **Teste e verificação do conjunto de réplicas**

`docker exec -it mongo1 mongosh --eval "rs.status()"` 

### Se conectar via MongoDB Compass

- Inserir dados no nó primário:

```json
use cliente

db.cliente.insertOne({codigo:1, nome: "Gustavo"});
db.cliente.insertOne({codigo:2, nome: "Bruno"});
db.cliente.insertOne({codigo:3, nome: "Thiago"});
db.cliente.insertOne({codigo:4, nome: "Idalécio"});
db.cliente.insertOne({codigo:5, nome: "Otavio"});
db.cliente.insertOne({codigo:6, nome: "Saito"});

db.cliente.find()
```

### Derrubar nó primário

`docker stop mongo1` 

`docker exec -it mongo2 mongosh --eval "rs.status()"` 

### Reestabelecer nó primário

`docker run -d --rm -p 27017:27017 --name mongo1 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo1` 

`docker exec -it mongo2 mongosh --eval "rs.status()"` 

### Derrubar e reestabelecer nó secundário

`docker stop mongo2`

`docker run -d --rm -p 27018:27017 --name mongo2 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo2`
