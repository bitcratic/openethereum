FROM alpine:edge AS builder

# show backtraces
ENV RUST_BACKTRACE 1

RUN apk add --no-cache \
  build-base \
  cargo \
  cmake \
  eudev-dev \
  linux-headers \
  perl \
  rust \
  git \
  clang-dev \
  llvm-dev

WORKDIR /openethereum
COPY . /openethereum
ENV C=clang CXX=clang++
RUN cargo build --release --features final --target x86_64-alpine-linux-musl --verbose
RUN strip target/x86_64-alpine-linux-musl/release/openethereum

FROM alpine:edge

# show backtraces
ENV RUST_BACKTRACE 1

# curl is installed to help create health and readiness checks on Kubernetes
RUN apk add --no-cache \
  libstdc++ \
  eudev-libs \
  libgcc \
  curl

RUN addgroup -g 1000 openethereum \
  && adduser -u 1000 -G openethereum -s /bin/sh -D openethereum

USER openethereum

EXPOSE 8080 8545 8180

WORKDIR /home/openethereum

RUN mkdir -p /home/openethereum/.local/share/io.parity.ethereum/
COPY --chown=openethereum:openethereum --from=builder /openethereum/target/x86_64-alpine-linux-musl/release/openethereum ./

ENTRYPOINT ["./openethereum"]
