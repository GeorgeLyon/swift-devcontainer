FROM ubuntu:22.04

# Prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install Swift compiler build dependencies
# Based on https://github.com/swiftlang/swift-docker/blob/main/swift-ci/main/ubuntu/22.04/Dockerfile
RUN apt-get update && apt-get install -y \
    # Build tools
    build-essential \
    cmake \
    ninja-build \
    pkg-config \
    git \
    curl \
    # LLVM/Clang toolchain
    clang \
    lld \
    libc++-dev \
    libc++abi-dev \
    # Development libraries
    libcurl4-openssl-dev \
    libedit-dev \
    libicu-dev \
    libncurses5-dev \
    libpython3-dev \
    libsqlite3-dev \
    libxml2-dev \
    libz3-dev \
    zlib1g-dev \
    # Python (required for build scripts)
    python3 \
    python3-pip \
    python3-six \
    python3-distutils \
    python3-pkg-resources \
    python3-psutil \
    # Additional utilities
    file \
    icu-devtools \
    rsync \
    swig \
    systemtap-sdt-dev \
    tzdata \
    uuid-dev \
    zip \
    unzip \
    gnupg2 \
    elfutils

# Make lld the default linker (required for Swift on aarch64 due to PIC relocation issues with GNU ld)
RUN ln -sf /usr/bin/ld.lld /usr/bin/ld

# Install sccache for faster builds (optional but recommended)
RUN curl -L https://github.com/mozilla/sccache/releases/download/v0.8.1/sccache-v0.8.1-aarch64-unknown-linux-musl.tar.gz | tar xz \
    && mv sccache-v0.8.1-aarch64-unknown-linux-musl/sccache /usr/local/bin/ \
    && rm -rf sccache-v0.8.1-aarch64-unknown-linux-musl

# Configure sccache cache size
ENV SCCACHE_CACHE_SIZE="80G"

# Install Swift (required for non-bootstrapped compilation)
# A prebuilt Swift toolchain is recommended for building portions of the Swift compiler written in Swift
# See: https://github.com/swiftlang/swift/blob/main/docs/HowToGuides/GettingStarted.md#linux
ARG SWIFT_VERSION=6.2.1
ARG SWIFT_PLATFORM=ubuntu22.04-aarch64
RUN SWIFT_URL="https://download.swift.org/swift-${SWIFT_VERSION}-release/ubuntu2204-aarch64/swift-${SWIFT_VERSION}-RELEASE/swift-${SWIFT_VERSION}-RELEASE-${SWIFT_PLATFORM}.tar.gz" \
    && curl -fsSL "$SWIFT_URL" -o swift.tar.gz \
    && tar -xzf swift.tar.gz --strip-components=2 -C /usr \
    && rm swift.tar.gz \
    && swift --version

# Claude Code is installed in ~/.local/bin
ENV PATH="/root/.local/bin:$PATH"

# Install Claude Code
ENV CLAUDE_CONFIG_DIR="/root/.claude-code-config"
RUN curl -fsSL https://claude.ai/install.sh | bash