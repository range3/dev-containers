FROM mcr.microsoft.com/vscode/devcontainers/cpp:0-bullseye

ENV EDITOR vim
ENV CPM_SOURCE_CACHE ~/.cache/CPM
ENV SPACK_ROOT=/home/vscode/.cache/spack

SHELL ["/bin/bash", "-c"]

RUN \
  # Common packages
  export DEBIAN_FRONTEND=noninteractive \
  && apt-get update \
  && apt-get -y install --no-install-recommends \
    pkg-config \
    vim \
    bash-completion \
    gfortran-10 \
    clang \
    clang-format \
    clang-tidy \
    clang-tools \
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

USER vscode
RUN \
  # spack
  git clone -c feature.manyFiles=true https://github.com/spack/spack.git "${SPACK_ROOT}" \
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
