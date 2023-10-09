# [1/13]
FROM ubuntu:20.04
LABEL maintainer="Eduardo Ferrari"

# Set to use bash to build the image instead of sh
SHELL ["/bin/bash", "-c"]

#Update/Upgrade [2/13]
RUN apt update -y && apt upgrade -y

# Configure the timezone and avoid extra questions during the installation [3/13]
RUN DEBIAN_FRONTEND="noninteractive" TZ="Europe/Stockholm" apt-get install -y tzdata

# Install helper tools [4/13]
RUN apt install -y wget curl git npm lsb-release

# Register MS Repositories [5/13]
RUN declare repo_version=$(if command -v lsb_release &> /dev/null; then lsb_release -r -s; else grep -oP '(?<=^VERSION_ID=).+' /etc/os-release | tr -d '"'; fi) && \
  # Download Microsoft signing key and repository
  wget https://packages.microsoft.com/config/ubuntu/$repo_version/packages-microsoft-prod.deb -O packages-microsoft-prod.deb && \
  # Install Microsoft signing key and repository
  dpkg -i packages-microsoft-prod.deb && \
  # Clean up
  rm packages-microsoft-prod.deb && \
  # Update packages
  apt update

# dotnet 6 [6/13]
RUN apt install -y dotnet-sdk-6.0

# az cli [7/13]
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

# func [8/13]
RUN curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg && \
  mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg && \
  sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list' && \
  apt update && \
  apt install -y azure-functions-core-tools-4

# powershell [9/13]
RUN apt install -y powershell

# Bicep
# Fetch the latest Bicep CLI binary [10/13]
RUN curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64 && \
  # Mark it as executable
  chmod +x ./bicep && \
  # Add bicep to your PATH
  mv ./bicep /usr/local/bin/bicep

# Install Powershell Modules [11/13]
RUN pwsh -c "Install-Module -name Az -AllowClobber -Force"

# Copy and Install the start-up scripts [12/13]
COPY ./pwsh-setup/ powershell
# [13/13]
RUN pwsh -File ./powershell/setupPowerShell.ps1 && rm -rf ./powershell

CMD [ "pwsh" ] 
