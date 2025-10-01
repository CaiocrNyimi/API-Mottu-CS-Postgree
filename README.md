
# 🏍️ API de Gestão de Motos:  
- Henzo Puchetti - RM555179
- Luann Domingos Mariano - RM558548
- Caio Cesar Rosa Nyimi - RM556331

---

## 📌 Descrição

Com uma arquitetura simples e eficiente para facilitar manutenção e escalabilidade, desenvolvemos uma:
API RESTful para gerenciamento de motos, pátios e suas movimentações, desenvolvida em ASP.NET Core com Entity Framework Core e banco Postgre. Permite operações CRUD completas, consultas parametrizadas de moto por ID e placa, e oferece documentação automática via Swagger.

---

## Rotas da API

### (Motos)

| Método | Endpoint               | Descrição                        | Códigos HTTP Esperados                         |
|--------|------------------------|----------------------------------|------------------------------------------------|
| GET    | /api/motos             | Retorna todas as motos           | 200 OK                                         |
| GET    | /api/motos/{id}        | Retorna moto por ID              | 200 OK, 404 Not Found                          |
| GET    | /api/motos/search      | Retorna moto pela placa (query)  | 200 OK, 404 Not Found                          |
| POST   | /api/motos             | Cria uma nova moto               | 201 Created, 400 Bad Request                   |
| PUT    | /api/motos/{id}        | Atualiza uma moto existente      | 204 No Content, 400 Bad Request, 404 Not Found |
| DELETE | /api/motos/{id}        | Exclui uma moto por ID           | 204 No Content, 404 Not Found                  |

### (Patios)

| Método | Endpoint               | Descrição                        | Códigos HTTP Esperados                         |
|--------|------------------------|----------------------------------|------------------------------------------------|
| GET    | /api/patios            | Retorna todos os pátios          | 200 OK                                         |
| GET    | /api/patios/{id}       | Retorna pátio por ID             | 200 OK, 404 Not Found                          |
| POST   | /api/patios            | Cria um novo pátio               | 201 Created, 400 Bad Request                   |
| PUT    | /api/patios/{id}       | Atualiza um pátio existente      | 204 No Content, 400 Bad Request, 404 Not Found |
| DELETE | /api/patios/{id}       | Exclui um pátio por ID           | 204 No Content, 404 Not Found                  |

### (Movimentacoes)

| Método | Endpoint               | Descrição                        | Códigos HTTP Esperados                         |
|--------|------------------------|----------------------------------|------------------------------------------------|
| GET    | /api/movimentacoes     | Retorna todas movimentações      | 200 OK                                         |
| GET    | /api/movimentacoes/{id}| Retorna movimentação por ID      | 200 OK, 404 Not Found                          |
| POST   | /api/movimentacoes     | Cria nova movimentação           | 201 Created, 400 Bad Request                   |
| PUT    | /api/movimentacoes/{id}| Atualiza movimentação existente  | 204 No Content, 400 Bad Request, 404 Not Found |
| DELETE | /api/movimentacoes/{id}| Exclui movimentação por ID       | 204 No Content, 404 Not Found                  |

---

## 🚀 Instalação e Execução

### ✅ Pré-requisitos

- .NET 7 SDK  
- Oracle Database (local ou remoto)  
- Visual Studio 2022 / VS Code

### 🔧 Configuração do Banco de Dados

No arquivo `.env`, configure a string e os dados de conexão Postgre:

```json
POSTGRES_DB=(nome do banco)
POSTGRES_USER=(usuario)
POSTGRES_PASSWORD=(senha)
DB_CONNECTION="Host=db;Database=(nome do banco);Username=(usuario);Password=(senha)"
```

Execute as migrations para criar as tabelas no banco:

```bash
dotnet ef migrations add InitialCreate
dotnet ef database update
```

### ▶️ Executando a Aplicação Localmente

- Abra a solução no Visual Studio ou VS Code.  
- Configure `MottuApi` como projeto de inicialização.  
- Execute (`Ctrl + F5` ou `dotnet run`).  
- Acesse a API via navegador ou Postman em:  
  `https://localhost:{porta}/swagger` (interface Swagger para testes).
  *EU RODEI NA URL* - `http://localhost:5248/swagger`
---

### ▶️ Executando a Aplicação na Nuvem

Guia Completo: Deploy da API ASP.NET Core 8.0 com PostgreSQL na Nuvem

- 1. Configuração do Projeto Local:
     
✅ Requisitos

.NET SDK 8.0
Docker Desktop
PostgreSQL
Editor de código

📁 Estrutura básica

Seu projeto deve conter:

/MottuApi
  ├── MottuApi.csproj
  ├── Program.cs
  ├── Controllers/
  ├── Models/
  ├── .env
  ├── Dockerfile
  
🔐 Arquivo .env

Crie um arquivo .env com a string de conexão:

env:

POSTGRES_DB=postgres
POSTGRES_USER=seu_usuario
POSTGRES_PASSWORD=sua_senha

DB_CONNECTION=Host=localhost;Database=postgres;Username=seu_usuario;Password=sua_senha;Ssl Mode=Disable;


🧪 Teste local:

dotnet run --project MottuApi.csproj

Acesse:

http://localhost:5248/swagger


☁️ 2. Criar Banco de Dados PostgreSQL na Nuvem (Azure):


🔧 Passos no Portal do Azure:

Vá para portal.azure.com

Crie um recurso: Banco de Dados PostgreSQL

Escolha:

Nome do servidor: nome_do_seu_servidor
Usuário: seu_usuario
Senha: sua_senha
Versão: PostgreSQL 16


Configure o firewall para permitir acesso da sua VM e da sua máquina local.


Atualize sua string de conexão:

env:

DB_CONNECTION="Host=nome_db.postgres.database.azure.com;Database=postgres;Username=seu_usuario;Password=sua_senha;Ssl Mode=Require;Trust Server Certificate=true;"


🐳 3. Preparar Docker para Deploy:

📄 Dockerfile

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY . .

RUN dotnet tool install --global dotnet-ef
ENV PATH="$PATH:/root/.dotnet/tools"

RUN dotnet restore MottuApi.csproj
RUN dotnet publish MottuApi.csproj -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app

RUN adduser --disabled-password --gecos "" appuser
USER appuser

COPY --from=build /app/publish .

EXPOSE 8080
ENTRYPOINT ["dotnet", "MottuApi.dll", "--urls", "http://+:8080"]



🖥️ 4. Deploy na VM Linux (Azure):


🔧 Acesse a VM via SSH

ssh usuario@ip-da-vm

🧱 Build da imagem

docker build -t mottuapi:latest .

🚀 Rodar o container com variável de ambiente

docker run -d \
  -e DB_CONNECTION="Host=nome_db.postgres.database.azure.com;Database=postgres;Username=seu_usuario;Password=sua_senha;Ssl Mode=Require;Trust Server Certificate=true;" \
  -p 5248:8080 \
  --name mottuapi \
  mottuapi:latest


🔓 5. Liberar porta no firewall da VM:

🔧 Via Azure CLI

az vm open-port --port 5248 --resource-group SeuGrupo --name SuaVM

🌐 Ou via Portal do Azure

Porta: 5248
Protocolo: TCP
Origem: Any
Ação: Allow
Prioridade: 380

🌐 6. Acessar a API via navegador

http://<ip-da-vm>:5248/swagger

Exemplo:
http://191.234.214.146:5248/swagger

🧹 7. Gerenciar o container

Parar:
docker stop mottuapi

Remover:
docker rm mottuapi
