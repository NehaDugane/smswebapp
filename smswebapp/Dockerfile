# =========================
# 1️⃣ Build stage
# =========================
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy project files
COPY . .

# Restore dependencies
RUN dotnet restore

# Build & publish
RUN dotnet publish smswebapp.csproj -c Release -o /app/publish

# =========================
# 2️⃣ Runtime stage
# =========================
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app

# Copy published output from build stage
COPY --from=build /app/publish .

# Expose application port
EXPOSE 8080

# Start application
ENTRYPOINT ["dotnet", "smswebapp.dll"]

