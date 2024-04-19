# Docker example for simple application

#### **Following this guide do add docker support**
https://learn.microsoft.com/en-us/visualstudio/containers/overview?view=vs-2022

This creates this dockerfile
```dockerfile
#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["TestSSR/TestSSR.csproj", "TestSSR/"]
COPY ["TestSSR.Client/TestSSR.Client.csproj", "TestSSR.Client/"]
RUN dotnet restore "./TestSSR/TestSSR.csproj"
COPY . .
WORKDIR "/src/TestSSR"
RUN dotnet build "./TestSSR.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./TestSSR.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "TestSSR.dll"]
```

Publishing the application pushes it to dockerhub

https://hub.docker.com/repository/docker/nephim/testssr/general


The application can then be run like so:
> docker run -p 8080:8080 nephim/testssr

You can also run this command and it will pull from docker hub.

While testing out you can just build your application with docker build

> docker build -t mycoolapp -f .\Dockerfile

