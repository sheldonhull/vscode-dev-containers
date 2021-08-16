FROM mcr.microsoft.com/vscode/devcontainers/universal:1-linux

# ** [Optional] Uncomment this section to install additional packages. **
# USER root
#
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>
#
# USER codespace
# ARG USERNAME=$USERNAME

# CACHED EXTENSIONS FOR FASTER STARTUP
USER root
RUN mkdir -p /home/codespace/.vscode-server/extensions \
    /home/codespace/.vscode-server/extensions && \
    chown -R codespace \
        /home/codespace/.vscode-server \
        /home/codespace/.vscode-server

COPY library-scripts/*.sh library-scripts/*.env /tmp/library-scripts/
RUN if [ "$INSTALL_AZURE_CLI" = "true" ]; then bash /tmp/library-scripts/azcli-debian.sh; fi \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts
RUN echo "✅ install chezmoi for dotfiles syncing" && sh -c "$(curl -fsLS git.io/chezmoi)" && mv ./bin/chezmoi /usr/local/bin/chezmoi && rm -rf ./bin

USER codespace
RUN pwsh -nologo -c 'New-Item -Path ($Profile | Split-Path -Parent) -ItemType Directory'
RUN echo "installing gitversion for automatic semver versioning" && dotnet tool install --global GitVersion.Tool
RUN curl -fsSL https://starship.rs/install.sh | sudo bash -s -- --force && echo "completed setup of starship.rs"
RUN echo "✅ installing task runners" \
  && go version \
  && go install github.com/magefile/mage@latest && go install github.com/iwittkau/mage-select@latest \
  && go install github.com/go-task/task/v3/cmd/task@latest

# Set multiple labels at once, using line-continuation characters to break long lines
LABEL vendor=misc \
      type=codespaces \
      is-beta=true \
      user=codespace \
      is-production=false \
      version="0.0.1-beta" \
      release-date="2021-06-01" \
      description="development image with tooling for vscode codespaces" \
      maintainer="sheldonhull" \
      name=codespaces-general
