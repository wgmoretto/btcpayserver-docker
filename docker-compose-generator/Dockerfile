# Primeira etapa da construção usando a imagem de SDK específica para compilação
# Definindo a plataforma para garantir compatibilidade cruzada
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:8.0.101-bookworm-slim AS builder

# Configurando o diretório de trabalho no container de construção
WORKDIR /source

# Copiando apenas o arquivo de projeto .csproj inicialmente para aproveitar o cache de camadas do Docker
COPY src/docker-compose-generator.csproj docker-compose-generator.csproj

# Restaurando as dependências especificadas no arquivo .csproj
RUN dotnet restore docker-compose-generator.csproj

# Copiando os demais arquivos do projeto para o container
COPY src/ .

# Publicando o aplicativo para o diretório /app em modo Release
RUN dotnet publish -c Release -o /app

# Segunda etapa da construção usando a imagem de Runtime para executar o aplicativo
FROM mcr.microsoft.com/dotnet/runtime:8.0.1-bookworm-slim

# Definindo metadados via rótulos (opcional)
LABEL org.btcpayserver.image=docker-compose-generator

# Configurando variáveis de ambiente necessárias
ENV INSIDE_CONTAINER=1
ENV APP_DATADIR=/datadir

# Definindo o diretório de trabalho para /app
WORKDIR /app

# Criando um volume apontando para /datadir
VOLUME /datadir

# Copiando o aplicativo publicado do container de construção para o container de runtime
COPY --from=builder /app .

# Definindo o ponto de entrada para o container
ENTRYPOINT ["dotnet", "docker-compose-generator.dll"]
