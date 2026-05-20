# See https://aka.ms/vscode-remote/devcontainer.json for config reference
FROM mcr.microsoft.com/devcontainers/base:ubuntu

# Install C++, CMake, Ninja, and PlantUML dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        ninja-build \
        openjdk-17-jre-headless \
        graphviz \
        wget \
    && rm -rf /var/lib/apt/lists/*

# Install PlantUML
RUN wget -qO /usr/share/plantuml/plantuml.jar https://github.com/plantuml/plantuml/releases/download/v1.2024.4/plantuml.jar \
    && chmod 644 /usr/share/plantuml/plantuml.jar

# Ensure the vscode user exists and has the proper home directory
USER vscode
