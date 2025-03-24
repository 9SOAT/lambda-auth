# Lambda de Autenticação AWS

Este repositório contém uma AWS Lambda desenvolvida em **Java 21** como parte da Fase 3 da Postech em Software Architecture da FIAP e realiza a autenticação de usuários através de **CPF**, validando os dados armazenados no **Amazon DynamoDB** e gerando um token JWT para autenticação.

---

## Tecnologias Utilizadas

- **Java 21** - Linguagem principal
- **AWS Lambda** - Serviço de computação serverless
- **Amazon DynamoDB** - Banco de dados NoSQL
- **API Gateway** - Exposição da API
- **AWS IAM** - Controle de permissões
- **Terraform** - Infraestrutura como código (IaC)
- **JSON Web Token (JWT)** - Geração de tokens de autenticação

---

## Como Rodar Localmente

### 1. Configuração Inicial

Certifique-se de ter o **Java 21**, **Maven** e o **AWS CLI** instalados e configurados.

### 2. Clonar o repositório

```sh
 git git@github.com:9SOAT/lambda-auth.git
```

### 3. Configurar credenciais da AWS

Se ainda não configurou suas credenciais AWS, faça:

```sh
 aws configure
```

E informe:
- **AWS Access Key ID**
- **AWS Secret Access Key**
- **Região AWS** (exemplo: `us-east-1`)

### 4. Instalar dependências e compilar o projeto

```sh
 mvn clean package
```

### 5. Testar localmente a Lambda

Usando a AWS SAM CLI:

```sh
 sam local invoke AuthWithDynamoDB --event event.json
```

Ou com o Java:

```sh
 java -jar target/lambda-1.0-SNAPSHOT.jar
```

> Substitua `event.json` por um JSON com um CPF válido para teste.

---

## Como Funciona

1. O cliente faz uma requisição `POST /login` para a API Gateway.
2. O API Gateway encaminha a requisição para a Lambda de autenticação.
3. A Lambda verifica o CPF informado no DynamoDB.
4. Se o CPF existir, gera um **JWT** com o `consumer_id` do usuário.
5. O token JWT é retornado ao cliente.
6. O cliente pode usar o token para a criação de um carrinho/pedido na API de Fast Food.

---

## Deploy com Terraform

O Terraform gerencia o deploy da Lambda, o armazenamento no **S3**, permissões **IAM** e a configuração do **API Gateway**.

### 1. Inicializar Terraform

```sh
 cd terraform
 terraform init
```

### 2. Aplicar Mudanças

```sh
 terraform apply --auto-approve
```

### 3. Obter a URL da API Gateway

Após o deploy, a URL da API será exibida como output:

```sh
Outputs:
api_url = "https://<id>.execute-api.us-east-1.amazonaws.com/login"
```

---

## Exemplo de Requisição

### Requisição HTTP

```sh
curl -X POST https://<id>.execute-api.us-east-1.amazonaws.com/login \
     -H "Content-Type: application/json" \
     -d '{"cpf": "12345678901"}'
```

### Resposta Esperada

```json
{
  "token": "Bearer eyJhbGciOiJIUzI1NiIsIn..."
}
```

---

## Estrutura do Repositório

```
/
├── lambda/               # Código-fonte da Lambda
│   ├── src/main/java/com/fiap/challenge/food/lambda/
│   │   └── AuthWithDynamoDB.java
│   ├── pom.xml           # Dependências do Maven
│   └── target/           # Build da Lambda
├── terraform/            # Terraform para infraestrutura
│   └── main.tf           # Configuração da Lambda e API Gateway
├── README.md             # Documentação
└── .github/workflows/    # Arquivos ignorados no Git
    ├── deploy.yaml       # CI/CD para deploy da Lambda na AWS quando um pull request é mergeado na main
    └── destroy.yaml      # Workflow para destruir a Lambda na AWS
```
