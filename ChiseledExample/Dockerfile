#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

# This stage will be used for debugging and as such is based on the full Ubuntu image
FROM mcr.microsoft.com/dotnet/runtime:8.0 AS debug
USER app
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["ChiseledExample/ChiseledExample.csproj", "ChiseledExample/"]
RUN dotnet restore "./ChiseledExample/./ChiseledExample.csproj"
COPY . .
WORKDIR "/src/ChiseledExample"
RUN dotnet build "./ChiseledExample.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./ChiseledExample.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage will be used for production and as such is based on the chiseled Ubuntu image
FROM mcr.microsoft.com/dotnet/runtime:8.0-jammy-chiseled AS final
USER app
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ChiseledExample.dll"]