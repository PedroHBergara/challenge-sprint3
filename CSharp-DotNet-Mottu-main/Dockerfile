FROM mcr.microsoft.com/dotnet/sdk:8.0-alpine AS build
WORKDIR /src

# Copiar arquivos de projeto e restaurar dependências
# Isso otimiza o cache do Docker, pois o restore só é refeito se o .csproj mudar
COPY ["challengeABD/*.csproj", "challengeABD/"]
RUN dotnet restore "challengeABD/challengeABD.csproj"

# Copiar todo o código fonte (assumindo que um .dockerignore está configurado)
COPY . .

# Definir o diretório de trabalho para a pasta do projeto
WORKDIR "/src/challengeABD"

# Construir a aplicação em modo Release
RUN dotnet build "challengeABD.csproj" -c Release -o /app/build

# Publicar a aplicação para o diretório /app/publish
RUN dotnet publish "challengeABD.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Estágio final de execução
FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine AS final
WORKDIR /app

# Expor as portas que a aplicação irá escutar
EXPOSE 5000
EXPOSE 5001

# Criar usuário não-root para segurança
RUN addgroup -g 1001 -S appgroup && \
    adduser -S appuser -u 1001 -G appgroup

# Definir o usuário não-root para executar a aplicação
USER appuser

# Copiar os artefatos publicados do estágio de build para o estágio final
COPY --from=build /app/publish .

# Definir o ponto de entrada da aplicação
# Substitua 'challengeABD.dll' pelo nome real do arquivo DLL principal da sua aplicação
ENTRYPOINT ["dotnet", "challengeABD.dll"]
