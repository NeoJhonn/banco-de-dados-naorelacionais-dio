# MongoDB e Bancos de Dados NoSQL

## Introdução

- 1970: BD Relacional.
- 1998: 1º Introdução NoSQL(No Sequel). 
- Reaparição NoSQL.
- Sem schema de criação, você pode criar a estrutura do banco da forma que achar melhor.
- A performace vai depender do tamanho do seu cluster e da latência da sua rede.
- Trasações => Base(Basically Available, Soft-state, Eventually Consistent).
- Características e vantagens: flexibilidade, escalabilidade, alta performace.

## Tipos de banco NoSQL

- Graph Store: são estruturas matemáticas são compostas entre nós(são os dados) e vértices(label).
  - Neo4j, usa a linguagem seifer como estruturação dos dados, testar no Docker.
  - Testar usando o Sandbox: https://sandbox.neo4j.com/ , acesse, crie uma conta, crie um projeto "Blank Sandbox". 
  - Comando para criar um nó:
```
CREATE (:Cliente {name: "Jhonny Azevedo", age: 41, hobbies: ['Programar, fazer pão caseiro']});
```
  - Comando para consulta:
```
MATCH (todos) RETURN todos;
```
  - Comando criar nó com relacionamento:
```
CREATE (:Cliente {name: "Mylena Moraes", age: 35, hobbies: ['Assistir Dorama']}) -[:Bloqueado]-> 
(:Cliente {name: "Mayara Moraes", age: 38, hobbies: ['Assistir Dorama']});
```
  - Comando adicionar relacionamento a um nó já existente:
```
MATCH (mylena:Cliente {name: "Mylena Moraes"}), (jhonny:Cliente {name: "Jhonny Azevedo"}) CREATE (jhonny)-[:Marido]->(mylena);
```
  - Comando excluir um relacionamento:
```
MATCH (mylena:Cliente {name: "Mylena Moraes"})-[relacionamento:Bloqueado]-() DELETE relacionamento;
```
  - Comando excluir um nó:
```
MATCH (mayara:Cliente {name: "Mayara Moraes"}) DELETE mayara;
```
  - Comando atualizar um nó
```
MATCH (mylena:Cliente {name: "Mylena Moraes"}) SET mylena.hobbies = ['Assistir Dorama, tricotar, crochê'];
```
  - Comando atualizar uma label:
```
MATCH (jhonny:Cliente {name: "Jhonny Azevedo"}) SET jhonny:Cliente_Especial;
``` 

- Wide-Columm Store: usar se tiver um volume maior de leitura do que de escrita, necessidade de fazer a consulta pela chave primária.
  - Cassandra usa o CQL(Cassandra Query Language).
  - Comando para criação de um banco de dados:
```
CREATE KEYSPACE database_test WITH  REPLICATION = {'class' : 'SimpleStrategy', 'replication_factor' : 1};
```
  - Comando entrar no KEYSPACE: ``use database_test;``

  - Comando para criar uma família de colunas(como se fosse uma tabela tradicional):
```
CREATE COLUMNFAMILY clients (name TEXT PRIMARY KEY, age int);
```

  - Comando fazer consultas: SELECT * FROM clients;

  - comando inserir um dado: 
```
INSERT INTO clients (name, age) VALUES('Jhonny Azevedo', 41);

ou 

INSERT INTO clients JSON '{"name": "Patrick}';

  - Comando para consultar um timestamp:
```
SELECT age, WRITETIME(age) FROM clients;
```

  - Comando para que o retorno de uma consulta seja na forma de JSON:
```
SELECT JSON * FROM clients;

  - Comando para atualizar uma coluna:
```
UPDATE clients SET age=33 WHERE name='Jhonny Azevedo';
```

  - Comando para adicionar uma nova coluna
```
ALTER COLUMNFAMILY clients ADD hobby text;
```

  - Comando para excluir um registro
```
DELETE FROM clients WHERE name='Jhonny Azevedo';
```

- Key-Value Store: constituido por duas partes, uma chave única(key) e um valor(value).
  - Redis disponível para teste em: https://try.redis.io

  - Comando para inserir um registro:
```
SET user1:name "Jhonny Azevedo"
```

  - Comando consultando pela key(chave)
```
GET user1:name
```
  - Comando inserir como JSON:
```
Set user '{"name": "Patrick", "age": 31}'
```

  - Criando um registro com expiração(útil para cache):
```
Set user2:name "Lula Molusco" EX 10 <--------------- expira em 10 segundo
```

  - Comando que verifica se um registro existe:
```
EXISTS user1:name
```

  - Comando inserir registros numa lista:
```
LPUSH user1:hobbies "Fazer pão" <----- hobbies
```

  - Comando para consultar pelo índice:
```
LINDEX user:hobbies 0 <------------ pega o valor que está nesse índice dentro de hobbies
```

  - Comando para listar valores num range ou indice específico:
```
LRANGE user1:hobbies 0 1 <------------ intervalo entre 0 e 1
```

  - Comando para indentificar o tipo de valor dentro de uma chave:
```
TYPE user1:name
```

  - Comando para inserir um tempo de expiração para um registro:
```
TTL user1:name "Patrick" EX 60 <----- expira em 60s
TTL user1:name <------ consulta tempo de expiração
```

  - Comando para remover o tempo de expiração:
```
PERSIST user2:name
```

  - Comando para excluir um dado/registro:
```
DEL user2:name
```

## Document Store
 
- Dados e documentos autocontidos e auto descritivos. Permite redundância e incosistência. Livre de esquemas, podendo utilizar
JSON, XML e outros, exemplo de DB MongoDB.

# MongoDB

- Criar um Docker Compose: arquivo onde se especifica as propriedades de cada container.
Documentação disponível em: https://docs.docker.com/compose/compose-file/
ex:
```
vi docker-compose.yml

services: 
         db:
           image: mongo
           container_name: db
           restart: always
           environment:
             - MONGO_INITDB_ROOT_USERNAME=dio
             - MONGO_INITDB_ROOT_PASSWORD=dio
           ports:
             - "27017: 27017"
           volumes:
             - /Users/seuNomedeUsuarioDoSeuPC/DIO/dbdata:/data/db
```
 
- Comando para rodar a imagem: `` docker-compose up -d ``

- Comando para mostrar as imgagens que estão rodando(ver as postas que estão sendo usadas): ``docker container ps``

- Comando para se conectar a imagem do mongo: ``mongo --host <o ip local do seu pc>:27017 -p dio -u dio``

- Comando que monstra os databases do Mongo: ``show databases``

- Studio 3T, ferramenta gráfica para manipular o Mongo, disponível em: https://studio3t.com/

## Schema Design 

- Embedding: é você ter o seu documento totalmente autocontigo, todas informações dentro dele, sem referênciar outras colections. 

- Referência: buscar referências em outros documentos/colections.

## Boa Práticas

- Evitar documentos muito grandes.

- Use nome, campos objetivos e curtos.

- Analise as suas queries utilizando explain().

- Atualize apenas os campos alterados.

- Evite negações em queries.

- Listas/Arrays dento dos documentos não podem crescer sem limit.

## JSON vs BSON

- BSON: é uma serialização codificada em binário de documentos semelhantes ao JSON. Contém extenções que permitem a apresentação de tipos
de dados que não fazem parte da especificação JSON, por exemplo BSON tem um tipo date, ObjectID.

- JSON: armazena apenas os dados primitivos, somente string e números.



































