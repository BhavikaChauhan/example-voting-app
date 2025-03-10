# because of dotnet, we always build on amd64, and target platforms in CLI
# dotnet doesn't support QEMU for building or running.
# (errors common in arm/v7 32bit) https://github.com/dotnet/dotnet-docker/issues/1537
# https://hub.docker.com/_/microsoft-dotnet
# hadolint ignore=DL3029
# to build for a different platform than your host, use --platform=<platform>
# for example, if you were on Intel (amd64) and wanted to build for ARM, you would use:
# docker buildx build --platform "linux/arm64/v8" .

# Use a specific platform explicitly to avoid undefined BUILDPLATFORM issues
FROM --platform=linux/amd64 mcr.microsoft.com/dotnet/sdk:7.0 AS build

# Define build arguments
ARG TARGETPLATFORM
ARG TARGETARCH
ARG BUILDPLATFORM
RUN echo "I am running on $BUILDPLATFORM, building for $TARGETPLATFORM"

# Set working directory for the source code
WORKDIR /source

# Copy project files and restore dependencies
COPY *.csproj . 

# Fix: Removed the invalid '-a $TARGETARCH' argument
RUN dotnet restore

# Copy remaining source code and publish the application
COPY . .
RUN dotnet publish -c Release -o /app --self-contained false --no-restore

# Create the final runtime image
FROM mcr.microsoft.com/dotnet/runtime:7.0
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "Worker.dll"]

