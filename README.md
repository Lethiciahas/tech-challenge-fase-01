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

## Discussão 12-Factor App

Os 12 princípios do 12-Factor App constituem um conjunto de boas práticas para o desenvolvimento de aplicações modernas, especialmente voltadas para ambientes de cloud computing e DevOps. Esses princípios visam padronizar a forma como aplicações são desenvolvidas, configuradas, implantadas e operadas, promovendo maior portabilidade, escalabilidade, resiliência e facilidade de manutenção. A adoção desses fatores torna a aplicação mais preparada para automação de deploy, integração contínua, escalabilidade horizontal e operação confiável em ambientes distribuídos.

Com base na análise do projeto desenvolvido, foram identificadas as seguintes oportunidades de melhoria, diretamente relacionadas aos princípios do 12-Factor App:

• Código-fonte versionado (Fator 1):criação de um repositório de versionamento para não perder o histórico de builds e alterações;

• Build, Release e Run (Fator 5): implementação de pipelines de CI/CD para automatizar e padronizar os processos de build, deploy e rollback;

• Processes – Aplicação Stateless (Fator 6): externalização do estado da aplicação por meio do uso de Redis para gerenciamento de sessões;

• Concurrency – Escalabilidade via Processos (Fator 8): adoção de load balancing e auto scaling para permitir escalabilidade automática conforme a demanda;

• Logs – Logs como Fluxo de Eventos (Fator 11): centralização e observabilidade de logs com ferramentas apropriadas, permitindo monitoramento e diagnóstico eficaz;

• Config – Configurações via Variáveis de Ambiente (Fator 3): evolução no uso de variáveis de ambiente com integração a serviços de gerenciamento de segredos, aumentando a segurança e governança.

Conclui-se que o ToggleMaster atende a diversos princípios fundamentais do 12-Factor App, especialmente aqueles relacionados a versionamento de código, dependências explícitas, uso de serviços externos e exposição por porta. Entretanto, para evoluir de um MVP para um ambiente de produção escalável e resiliente, torna-se essencial avançar na automação, observabilidade, segregação de ambientes e adoção de práticas cloud-native. A implementação dessas melhorias alinhará o projeto de forma mais completa aos princípios do 12-Factor App e às melhores práticas de DevOps.


## Integrantes:
    • Augusto Henrique Gomes Bitiano - rm369357
    • Guilherme Nogueira Salandini - rm369415
    • Nicolas Alves Ribeiro - rm369920
    • Lethicia Hethielly Andrade Silva - rm369413
    • Luciano Tadeu Pedroso Albano Pereira rm370158
