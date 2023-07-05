FROM mcr.microsoft.com/devcontainers/cpp:1-debian-12

ARG REINSTALL_CMAKE_VERSION_FROM_SOURCE="3.22.2"

# Optionally install the cmake for vcpkg
COPY ./reinstall-cmake.sh /tmp/

RUN if [ "${REINSTALL_CMAKE_VERSION_FROM_SOURCE}" != "none" ]; then \
        chmod +x /tmp/reinstall-cmake.sh && /tmp/reinstall-cmake.sh ${REINSTALL_CMAKE_VERSION_FROM_SOURCE}; \
    fi \
    && rm -f /tmp/reinstall-cmake.sh

ARG SPACK_BRANCH=develop
ENV SPACK_ROOT=/home/vscode/.cache/spack
ENV CPM_SOURCE_CACHE=/home/vscode/.cache/CPM
ENV TZ=Asia/Tokyo

RUN \
  # Common packages
  export DEBIAN_FRONTEND=noninteractive \
  && apt-get update \
  && apt-get install -y --no-install-recommends \
    ssh \
    less \
    tree \
    git \
    vim \
    make \
    apt-file \
  # locale
  && apt-get install -y --no-install-recommends \
    locales \
  && sed -i -E 's/# (en_US.UTF-8)/\1/' /etc/locale.gen \
  && sed -i -E 's/# (ja_JP.UTF-8)/\1/' /etc/locale.gen \
  && locale-gen \
  # timezone
  && apt-get install -y --no-install-recommends \
    tzdata \
  && ln -fs /usr/share/zoneinfo/${TZ} /etc/localtime \
  && dpkg-reconfigure tzdata \
  # Clean up
  && apt-get autoremove -y \
  && apt-get clean -y \
  && rm -rf /var/lib/apt/lists/*

ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:ja
ENV LC_ALL ja_JP.UTF-8
ENV EDITOR vim

SHELL ["/bin/bash", "-c"]
RUN \
  # Common packages
  export DEBIAN_FRONTEND=noninteractive \
  && apt-get update \
  && apt-get -y install --no-install-recommends \
    pkg-config \
    vim \
    bash-completion \
    gfortran \
    clang \
    clang-format \
    clang-tidy \
    clang-tools \
    cmake-format \
    iwyu \
    tree \
    file \
    environment-modules \
    libtbb-dev \
  # Clean up
  && apt-get autoremove -y \
  && apt-get clean -y \
  && rm -rf /var/lib/apt/lists/*

COPY spack.sh /etc/profile.d/03-spack.sh
ADD https://api.github.com/repos/spack/spack/git/refs/heads/${SPACK_BRANCH} /tmp/spack-version.json

USER vscode
RUN \
  # spack
  git clone -b $SPACK_BRANCH -c feature.manyFiles=true https://github.com/spack/spack.git "${SPACK_ROOT}" \
  && mkdir -p /home/vscode/.spack \
  && printf "%s\n" \
    '' \
    '# activate default spack env' \
    'if [ -n "$DOTFILES_DEFAULT_SPACK_ENV" ]; then' \
    '  if command -v spack 1>/dev/null 2>&1; then' \
    '    if spack env st | grep -q "No active environment"; then' \
    '      spack env activate "$DOTFILES_DEFAULT_SPACK_ENV"' \
    '    fi' \
    '  fi' \
    'fi' \
    >> /home/vscode/.profile
