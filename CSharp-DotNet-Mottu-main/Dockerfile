# -------------------------------
# Estágio de build
# -------------------------------
FROM mcr.microsoft.com/dotnet/sdk:8.0-alpine AS build
WORKDIR /src

# Copiar arquivos de projeto e restaurar dependências
COPY ["challengeABD/*.csproj", "challengeABD/"]
RUN dotnet restore "challengeABD/challengeABD.csproj"

# Copiar todo o código fonte
COPY . .

# Definir o diretório de trabalho para a pasta do projeto
WORKDIR "/src/challengeABD"

# Construir a aplicação em modo Release
RUN dotnet build "challengeABD.csproj" -c Release -o /app/build

# Publicar a aplicação
RUN dotnet publish "challengeABD.csproj" -c Release -o /app/publish /p:UseAppHost=false


# -------------------------------
# Estágio final
# -------------------------------
FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine AS final
WORKDIR /app

# Instalar pacotes de globalização no runtime
RUN apk add --no-cache icu-libs
ENV DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=false

# Expor porta esperada pelo Azure
EXPOSE 80

# Criar usuário não-root
RUN addgroup -g 1001 -S appgroup && \
    adduser -S appuser -u 1001 -G appgroup
USER appuser

# Copiar artefatos publicados
COPY --from=build /app/publish .

# Configurar a aplicação para escutar na porta 80
ENV ASPNETCORE_URLS=http://+:80

# Definir ponto de entrada
ENTRYPOINT ["dotnet", "challengeABD.dll"]
