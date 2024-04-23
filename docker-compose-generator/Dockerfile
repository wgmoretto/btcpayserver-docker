#
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:8.0.101-bookworm-slim AS builder
WORKDIR /source
COPY src/docker-compose-generator.csproj docker-compose-generator.csproj

# Dockerfile ajustado
# Usando a imagem base oficial do .NET
FROM mcr.microsoft.com/dotnet/sdk:latest

# Definindo o diretório de trabalho
WORKDIR /app

# Cache some dependencies
RUN dotnet restore

# Copiando o diretório src, certifique-se de que 'src' está no contexto de construção
# Se 'src' não existir, este comando falhará
COPY src/. .

# Publicando a aplicação para a pasta /app em modo Release
RUN dotnet publish --output /app/ --configuration Release

# Sua aplicação .NET agora está pronta para ser executada com a base da imagem runtime

#
FROM mcr.microsoft.com/dotnet/runtime:8.0.1-bookworm-slim
LABEL org.btcpayserver.image=docker-compose-generator
WORKDIR /datadir
WORKDIR /app
ENV APP_DATADIR=/datadir
VOLUME /datadir

ENV INSIDE_CONTAINER=1

COPY --from=builder "/app" .

ENTRYPOINT ["dotnet", "docker-compose-generator.dll"]
