ARG NODE_VERSION=22.21.1
ARG PYTHON_VERSION=3.13

FROM n8nio/runners:2.2.5 AS official_source_image

FROM python:${PYTHON_VERSION}-alpine

ARG N8N_VERSION=snapshot
ARG N8N_RELEASE_TYPE=dev

ENV NODE_ENV=production \
    N8N_RELEASE_TYPE=${N8N_RELEASE_TYPE} \
    SHELL=/bin/sh

# Bring `uv` over from python-runner-builder, to make the image easier to extend
COPY --from=official_source_image /usr/local/bin/uv /usr/local/bin/uv

# Bring node over from node-alpine
COPY --from=official_source_image /usr/local/bin/node /usr/local/bin/node

# libstdc++ is required by Node
# libc6-compat is required by task-runner-launcher
RUN apk add --no-cache ca-certificates tini libstdc++ libc6-compat ffmpeg

# Bring corepack and pnpm over, to make the image easier to extend
COPY --from=official_source_image /usr/local/lib/node_modules/corepack /usr/local/lib/node_modules/corepack
RUN ln -s ../lib/node_modules/corepack/dist/corepack.js /usr/local/bin/corepack && \
		ln -s ../lib/node_modules/corepack/dist/pnpm.js /usr/local/bin/pnpm

RUN addgroup -g 1000 -S runner \
 && adduser  -u 1000 -S -G runner -h /home/runner -D runner

WORKDIR /home/runner

COPY --from=official_source_image --chown=root:root /opt/runners/task-runner-javascript /opt/runners/task-runner-javascript
COPY --from=official_source_image --chown=root:root /opt/runners/task-runner-python /opt/runners/task-runner-python

COPY --from=official_source_image /usr/local/bin/task-runner-launcher /usr/local/bin/task-runner-launcher

### custom start ###
RUN cd /opt/runners/task-runner-javascript && pnpm add moment uuid
RUN cd /opt/runners/task-runner-python && uv pip install --no-cache-dir numpy pandas requests beautifulsoup4 yt-dlp
COPY --chown=root:root n8n-task-runners.json /etc/n8n-task-runners.json

USER runner

EXPOSE 5680/tcp
ENTRYPOINT ["tini", "--", "/usr/local/bin/task-runner-launcher"]
CMD ["javascript", "python"]

LABEL org.opencontainers.image.title="n8n task runners" \
      org.opencontainers.image.description="Sidecar image providing n8n task runners for JavaScript and Python code execution" \
      org.opencontainers.image.source="https://github.com/n8n-io/n8n" \
      org.opencontainers.image.url="https://n8n.io" \
      org.opencontainers.image.version="${N8N_VERSION}"
