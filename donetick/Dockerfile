ARG BUILD_FROM=ghcr.io/home-assistant/amd64-base:3.15
FROM $BUILD_FROM

WORKDIR /app

RUN apk --no-cache add gcc musl-dev git sqlite-dev curl jq
RUN mkdir -p /app/core

# Determine the architecture and download the appropriate binary
# TODO: am sure there is a better way to do this but this works for now
RUN arch=$(uname -m) && \
    case "$arch" in \
        x86_64) binary="donetick_Linux_x86_64.tar.gz" ;; \
        aarch64) binary="donetick_Linux_arm64.tar.gz" ;; \
        armv7l) binary="donetick_Linux_armv7.tar.gz" ;; \
        armv6l) binary="donetick_Linux_armv6.tar.gz" ;; \
        *) echo "Unsupported architecture: $arch" && exit 1 ;; \
    esac && \
    latest_release=$(curl --silent "https://api.github.com/repos/dawogid/donetick/releases/latest" | jq -r .tag_name) && \
    curl -fL "https://github.com/dawogid/donetick/releases/download/${latest_release}/${binary}" | tar -xz -C /app/core

# Copy entrypoint script
COPY entrypoint.sh /app/entrypoint.sh
RUN chmod +x /app/entrypoint.sh


# Expose necessary ports
EXPOSE 2021

# Set the entrypoint script
CMD ["/app/entrypoint.sh"]
