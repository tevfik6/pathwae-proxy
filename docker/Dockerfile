# BUILD Project with go image
FROM docker.io/golang:1.18
ARG VERSION=0.0.1

COPY . /go/src/pathwae
WORKDIR /go/src/pathwae
RUN set -eux; \
    echo "Building $VERSION..."; \
    CGO_ENABLED=0 go build -o pathwae -ldflags="-X main.Version=${VERSION}" ./cmd/pathwae/main.go; \
    strip pathwae; \
    chmod +x pathwae;

# Build the "ui" from node + yarn
FROM docker.io/node:alpine
COPY ./ui /app
WORKDIR /app
RUN yarn install
RUN yarn run build

# Create a scratch image with the go binary only
FROM scratch
COPY --from=0 /go/src/pathwae/pathwae /pathwae
COPY --from=1 /app/dist /web

VOLUME /certs
EXPOSE 80 443 8080
CMD ["/pathwae"]
LABEL maintainer="Patrice Ferlet <metal3d@gmail.com>" \
    description="PathWae is a web application to manage your path" \
    version="${VERSION}"
