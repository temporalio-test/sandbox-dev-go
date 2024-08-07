# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.231.6/containers/go/.devcontainer/base.Dockerfile
# https://github.com/microsoft/vscode-remote-try-go/blob/main/.devcontainer/devcontainer.json

ARG VARIANT="1-bookworm"
FROM mcr.microsoft.com/vscode/devcontainers/go:1-${VARIANT}

# [Optional] Uncomment this section to install additional OS packages.
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends curl gnupg git

# Install Temporal CLI
ARG TEMPORAL_CLI_VERSION=1.0.0
RUN PLATFORM=linux && \
    ARCHITECTURE=$(uname -m) && \
    case $ARCHITECTURE in \
        x86_64 | amd64) ARCHITECTURE=amd64 ;; \
        aarch64 | arm64) ARCHITECTURE=arm64 ;; \
        *) echo "Unsupported architecture: $ARCHITECTURE" && exit 1 ;; \
    esac && \
    curl -L "https://github.com/temporalio/cli/releases/download/v${TEMPORAL_CLI_VERSION}/temporal_cli_${TEMPORAL_CLI_VERSION}_${PLATFORM}_${ARCHITECTURE}.tar.gz" | tar -xz -C /usr/local/bin && \
    chmod +x /usr/local/bin/temporal

# Create the /temporal directory and make vscode the owner
RUN mkdir -p /temporal && \
        chown -R vscode: /temporal

# Prepare docker entrypoint
COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh

# Get the samples-go repository
USER vscode
RUN mkdir -p /temporal/samples && \
        git clone https://github.com/temporalio/samples-go.git /temporal/samples/go && \
        cd /temporal/samples/go && \
        go mod download

# Change WORKDIR to the samples
WORKDIR /temporal/samples/go

# Set entrypoint
ENTRYPOINT [ "/docker-entrypoint.sh" ]
CMD [ "sleep", "infinity" ]
