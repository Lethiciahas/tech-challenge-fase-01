# Tech challenge Fase 01

Projeto Togglemaster — Deployment com Docker, EC2 e RDS

Este projeto demonstra o deploy da aplicação togglemaster utilizando Docker, AWS EC2 e PostgreSQL no RDS, incluindo build da imagem, publicação no Docker Hub e testes das rotas da API.

## Tecnologias Utilizadas

- Docker / Docker Compose
- Python / Flask
- PostgreSQL (AWS RDS)
- AWS EC2
- Docker Hub

## Execução Local
### Clonando o Projeto
```bash
git clone https://github.com/dougls/toggle-master-monolith.git
cd toggle-master-monolith
```

1. Subir o projeto com Docker Compose
```bash
docker-compose up --build
```

2. Testar as rotas conforme o código do projeto [vide readme  https://github.com/dougls/toggle-master-monolith.git]:

Rotas disponíveis:

GET /flags

POST /flags

PUT /flags

GET /health

## Build e Push da Imagem

Após validar localmente, build a imagem para o repositorio dockerhub como no exemplo:
```bash
docker build -t repository/name:<tag> .
docker push repository/name:<tag>
```
## Deploy na EC2
1. Acesso à EC2
```bash
ssh -i ~/.ssh/<chave> ubuntu@<ip_publico>
```

2. Instalação de dependências:
```bash
sudo apt update
sudo apt install docker.io postgresql-client -y
sudo systemctl start docker
sudo systemctl enable docker
```

3. Conexão com o RDS:
```bash
psql -h <endpoint_rds> -U <usuario> -p 5432
```
Dentro do psql:
```bash
CREATE DATABASE togglemaster;
```

4. Pull da imagem na EC2:
```bash
sudo docker pull lethiciahas/toggle-lab:1.1
```

5. Criação do container da aplicação:
```bash
sudo docker run -d \
 --name toggle-lab \
 -p 5000:5000 \
 -e DB_HOST=<endpoint_rds> \
 -e DB_NAME=togglemaster \
 -e DB_USER=<usuario> \
 -e DB_PASSWORD=<password> \
 -e DB_PORT=5432 \
 lethiciahas/toggle-lab:1.1
 ```

6. Certificar inicialização do banco:
```bash
sudo docker ps -a
sudo docker exec -it <id_container> -- /bin/bash
flask init-db
```

### Testes da Aplicação
Health Check:
```bash
curl http://localhost:5000/health
```

No browser:
http://<ip_publico>:5000/health

Criar Flag:
```bash
curl -X POST \
 -H "Content-Type: application/json" \
 -d '{"name": "new-feature-13-01-2026", "is_enabled": true}' \
 http://localhost:5000/flags
 ```

Buscar Flag:
```bash
curl -X GET http://localhost:5000/flags/new-feature-13-01-2026
```

Visualizar no Browser:
http://<ip_publico>:5000/flags

### Observações

- Certifique-se de liberar a porta 5000 no Security Group da EC2.
- O RDS deve permitir acesso da EC2 na porta 5432.


## Integrantes:
    • Augusto Henrique Gomes Bitiano - rm369357
    • Guilherme Nogueira Salandini - rm369415
    • Nicolas Alves Ribeiro - rm369920
    • Lethicia Hethielly Andrade Silva - rm369413
    • Luciano Tadeu Pedroso Albano Pereira rm370158
