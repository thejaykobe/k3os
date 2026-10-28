ARG ALPINE_VERSION=3.23
FROM alpine:${ALPINE_VERSION}

# rancher-system-agent: a Rancher-managed agent that holds a websocket to
# Rancher Manager and executes Plans. Having this on the node means cert /
# cluster-token rotation is driven by Rancher's control plane rather than
# by replaying stale file-based import manifests on every boot (which was
# the k3s AddOn-controller clobber loop that broke cattle-cluster-agent on
# cert rotation).

ARG RANCHER_SYSTEM_AGENT_VERSION=v0.13.3
ARG ARCH

RUN apk add --no-cache curl

RUN set -eux; \
    mkdir -p /output; \
    curl -fsSL -o /output/rancher-system-agent \
      "https://github.com/rancher/system-agent/releases/download/${RANCHER_SYSTEM_AGENT_VERSION}/rancher-system-agent-${ARCH}"; \
    curl -fsSL -o /tmp/sha256sum.txt \
      "https://github.com/rancher/system-agent/releases/download/${RANCHER_SYSTEM_AGENT_VERSION}/sha256sum.txt"; \
    expected=$(awk -v f="rancher-system-agent-${ARCH}" '$2==f || $2=="./"f {print $1}' /tmp/sha256sum.txt); \
    [ -n "$expected" ] || { echo "no checksum entry for rancher-system-agent-${ARCH}"; exit 1; }; \
    actual=$(sha256sum /output/rancher-system-agent | awk '{print $1}'); \
    [ "$expected" = "$actual" ] || { echo "checksum mismatch: expected=$expected actual=$actual"; exit 1; }; \
    chmod +x /output/rancher-system-agent; \
    /output/rancher-system-agent --help >/dev/null 2>&1 || true
