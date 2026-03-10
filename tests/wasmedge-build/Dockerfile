FROM quay.io/fedora/fedora:latest

ARG WASMEDGE_VERSION="0.16.1"

# Install the deps for building crun (and WasmEdge from source when needed)
RUN dnf update -y && dnf install -y awk make python git gcc gcc-c++ automake autoconf libcap-devel \
		systemd-devel yajl-devel libseccomp-devel pkg-config diffutils \
		go-md2man glibc-static python3-libmount libtool buildah podman which \
		cmake ninja-build

# Install WasmEdge: use installer for releases, build from source for master
RUN if [ "$WASMEDGE_VERSION" = "master" ]; then \
		git clone --depth 1 https://github.com/WasmEdge/WasmEdge.git /tmp/WasmEdge && \
		cd /tmp/WasmEdge && \
		cmake -Bbuild -GNinja \
			-DCMAKE_BUILD_TYPE=Release \
			-DCMAKE_INSTALL_PREFIX=/usr \
			-DWASMEDGE_USE_LLVM=OFF \
			-DWASMEDGE_BUILD_TESTS=OFF \
			. && \
		cmake --build build && \
		cmake --build build --target install && \
		rm -rf /tmp/WasmEdge; \
	else \
		curl -sSf https://raw.githubusercontent.com/WasmEdge/WasmEdge/master/utils/install.sh | bash -s -- -p /usr -v $WASMEDGE_VERSION && \
		cp -P /usr/lib/libwasmedge* /usr/lib64/; \
	fi
RUN ldconfig

# Copy hello_wasm test files (hello.wasm is pre-built)
ADD hello_wasm /hello_wasm

COPY run-tests.sh /usr/local/bin

ENTRYPOINT ["/usr/local/bin/run-tests.sh"]
