FROM rockylinux:8.6

ENV LANG=en_US.UTF-8 \
    LC_ALL=en_US.utf8

ARG PYTHON_VERSION
ARG PIP_NO_CACHE_DIR=1
#ARG LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/lib/

#RUN dnf upgrade -y \
RUN set -eux \
    && set -o pipefail \
    && export __PYTHON_VERSION__="${PYTHON_VERSION}" \
    && export __PYTHON_BIN__="python${__PYTHON_VERSION__%.*}" \
    && dnf upgrade -y \
    && dnf install -y \
      bzip2-devel \
      ca-certificates \
      expat-devel \
      findutils \
      gcc \
      gdbm-devel \
      libffi-devel \
      libuuid-devel \
      make \
      openssl-devel \
      readline-devel \
      sqlite-devel \
      tk-devel \
      tzdata \
      wget \
      which \
      xz \
      xz-devel \ 
    && mkdir -p /usr/src/python/ \
    && cd /usr/src/python/ \
    && wget -O python.tar.xz "https://www.python.org/ftp/python/${__PYTHON_VERSION__}/Python-${__PYTHON_VERSION__}.tar.xz" \
    && tar -xf python.tar.xz \
    && cd Python-${PYTHON_VERSION} \
    && ./configure \
          --enable-optimizations \
          --enable-loadable-sqlite-extensions \
          --enable-optimizations \
          --enable-option-checking=fatal \
          --enable-shared \
          --with-lto \
          --with-system-expat \
          --with-ensurepip \
    && nproc="$(nproc)" \
    && make -j "$nproc" \
          LDFLAGS="-Wl,--strip-all" \
    && make altinstall \
    && cd / \
    && rm -rf /usr/src/python \
    && find /usr/local -depth \
          \( \
            \( -type d -a \( -name test -o -name tests -o -name idle_test \) \) \
            -o \( -type f -a \( -name '*.pyc' -o -name '*.pyo' -o -name 'libpython*.a' \) \) \
          \) -exec rm -rf '{}' + \
    && echo '/usr/local/lib/' > /etc/ld.so.conf.d/usrlocallib.conf \
    && ldconfig \
    && dnf mark remove \
          bzip2-devel \
          expat-devel \
          findutils \
          gcc \
          gdbm-devel \
          libffi-devel \
          libuuid-devel \
          make \
          openssl-devel \
          readline-devel \
          sqlite-devel \
          tk-devel \
          tzdata \
          wget \
          which \
          xz \
          xz-devel \ 
    && set +o pipefail \
    && find /usr/local -type f -executable -not \( -name '*tkinter*' \) -exec ldd '{}' ';' \
        | grep -vi "not found" \
        | awk '/=>/ { print $(NF-1) }' \
        | sort -u \
        | xargs -r rpm -qf 2>&1\
        | sort -u \
        | egrep -vi "not owned" \
        | xargs -r dnf mark install \
    && set -o pipefail \
    && dnf autoremove -y \
    && dnf clean all \
    && yum clean all \
    && rm -rf /var/cache/{yum,dnf} \
    && ${__PYTHON_BIN__} -m pip install --upgrade pip