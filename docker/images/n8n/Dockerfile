# Set the base image with the desired Node.js version
ARG NODE_VERSION=18.17.1
FROM n8nio:${NODE_VERSION}

# Required build arguments
ARG N8N_VERSION=1.6.1
RUN if [ -z "$N8N_VERSION" ] ; then echo "The N8N_VERSION argument is missing!" ; exit 1; fi

# Set environment variables
ENV N8N_VERSION=${N8N_VERSION}
ENV NODE_ENV=production

# Install n8n and its dependencies
RUN set -eux; \
    apkArch="$(apk --print-arch)"; \
    case "$apkArch" in \
        'armv7') apk --no-cache add --virtual build-dependencies python3 build-base;; \
    esac && \
    npm install -g --omit=dev n8n@${N8N_VERSION} && \
    case "$apkArch" in \
        'armv7') apk del build-dependencies;; \
    esac && \
    rm -rf /usr/local/lib/node_modules/n8n/node_modules/n8n-design-system && \
    rm -rf /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/node_modules && \
    find /usr/local/lib/node_modules/n8n -type f -name "*.ts" -o -name "*.js.map" -o -name "*.vue" | xargs rm && \
    rm -rf /root/.npm

# Copy the entrypoint script
COPY docker-entrypoint.sh /

# Create the .n8n directory and set ownership
RUN mkdir .n8n && chown node:node .n8n

# Switch to the 'node' user and set the entrypoint
USER node
ENTRYPOINT ["tini", "--", "/docker-entrypoint.sh"]
