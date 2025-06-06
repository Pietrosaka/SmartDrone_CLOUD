# 📦 API de Drones (.NET 6 + MySQL em Containers)

Este projeto é uma API RESTful desenvolvida em .NET 6 para o gerenciamento de drones. A aplicação realiza operações completas de CRUD (Create, Read, Update, Delete) e utiliza um banco de dados MySQL em container com persistência de dados.

---

## ✅ Requisitos Atendidos

- [x] API em .NET com CRUD completo  
- [x] Dockerfile personalizado (usuário não-root, diretório de trabalho, variáveis de ambiente)  
- [x] Aplicação exposta em uma porta configurável  
- [x] Container do MySQL com volume, variáveis de ambiente e porta exposta  
- [x] Banco de dados diferente do H2  
- [x] Testes de CRUD com arquivos JSON  

---

## 🐬 Subindo o Container do Banco de Dados (MySQL)

```bash
docker run -d   --name mysql-container   -e MYSQL_ROOT_PASSWORD=root123   -e MYSQL_DATABASE=meubanco   -e MYSQL_USER=meuusuario   -e MYSQL_PASSWORD=senha123   -v mysql_data:/var/lib/mysql   -p 3306:3306   mysql:8.0
```

---

## 🐳 Dockerfile para a Aplicação .NET

```Dockerfile
# Etapa base (runtime)
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 5000

# Etapa de build
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY . .
WORKDIR /src/SmartDrones.API
RUN dotnet restore
RUN dotnet publish -c Release -o /app/publish

# Etapa final
FROM base AS final
WORKDIR /app
RUN useradd -m appuser
USER appuser
ENV ASPNETCORE_ENVIRONMENT=Production
COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "SmartDrones.API.dll"]
```

---

## ⚙️ Build da Imagem da Aplicação

Na raiz do projeto, execute:

```bash
docker build -t smartdrones-api .
```

---

## 🚀 Execução do Container da Aplicação

Após o build, execute:

```bash
docker run -d \
  --name smartdrones-api \
  -e ConnectionStrings__DefaultConnection="Server=host.docker.internal;Port=3306;Database=meubanco;User=meuusuario;Password=senha123;" \
  -p 5000:5000 \
  smartdrones-api
```

---

## 📝 Observações

- Certifique-se de que as portas **3306** (MySQL) e **5000** (aplicação) estejam disponíveis.  
- **Não é necessário docker-compose**; os containers são executados individualmente com `docker run`.  
- Para verificar os logs da aplicação:

```bash
docker logs smartdrones-api
```
