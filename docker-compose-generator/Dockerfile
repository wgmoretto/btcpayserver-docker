# Utiliza a imagem de SDK do .NET especificada, definindo a plataforma para garantir compatibilidade cruzada
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:8.0.101-bookworm-slim AS builder

# Configura o diretório de trabalho no container para /source
WORKDIR /source

# Copia apenas o arquivo de projeto .csproj para o diretório /source para aproveitar o cache de camadas do Docker
COPY src/docker-compose-generator.csproj .

# Restaura as dependências especificadas no arquivo .csproj
RUN dotnet restore docker-compose-generator.csproj

# Certifique-se de que o caminho 'src/' está correto e que o diretório está acessível no contexto do build
# Se falhar, verifique o caminho do diretório ou o arquivo .dockerignore
COPY src/ .

# Publica o aplicativo para o diretório /app em modo Release
RUN dotnet publish -c Release -o /app

# Segunda etapa da construção usando a imagem de Runtime do .NET
FROM mcr.microsoft.com/dotnet/runtime:8.0.1-bookworm-slim

# Define metadados via rótulos (opcional)
LABEL org.btcpayserver.image=docker-compose-generator

# Configura variáveis de ambiente necessárias
ENV INSIDE_CONTAINER=1
ENV APP_DATADIR=/datadir

# Define o diretório de trabalho para /app
WORKDIR /app

# Cria um volume apontando para /datadir
VOLUME /datadir

# Copia o aplicativo publicado do container de construção para o container de runtime
COPY --from=builder /app .

# Define o ponto de entrada para o container
ENTRYPOINT ["dotnet", "docker-compose-generator.dll"]
