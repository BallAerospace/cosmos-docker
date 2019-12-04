# syntax = docker/dockerfile:1.0-experimental

FROM centos:7

RUN yum update -y && yum install -y \
  cmake \
  freeglut \
  freeglut-devel \
  gcc \
  gcc-c++ \
  gdbm-devel \
  git \
  gstreamer-plugins-base-devel \
  iproute \
  libyaml-devel \
  libffi-devel \
  make \
  openssl-devel \
  ncurses-devel \
  net-tools \
  qt4 \
  qt4-devel \
  readline-devel \
  wget \
  zlib-devel

# Only ruby 2.0 in Centos7 so build from source

# START MODIFIED CONTENT FROM OFFICIAL RUBY DOCKERFILE

# skip installing gem documentation
RUN set -eux; \
	mkdir -p /usr/local/etc; \
	{ \
		echo 'install: --no-document'; \
		echo 'update: --no-document'; \
	} >> /usr/local/etc/gemrc

ENV RUBY_MAJOR 2.5
ENV RUBY_VERSION 2.5.7
ENV RUBY_DOWNLOAD_SHA256 201870e8f58957d542233fef588b1d76f7bf962fea44dcbd2237f4a5899a3f95
ENV RUBYGEMS_VERSION 3.0.3

RUN --mount=type=secret,id=sslsecret set -eux; \
  export SSL_CERT_FILE=/run/secrets/sslsecret; \
	\
	wget -O ruby.tar.xz "https://cache.ruby-lang.org/pub/ruby/${RUBY_MAJOR%-rc}/ruby-$RUBY_VERSION.tar.xz"; \
	echo "$RUBY_DOWNLOAD_SHA256 *ruby.tar.xz" | sha256sum --check --strict; \
	\
	mkdir -p /usr/src/ruby; \
	tar -xJf ruby.tar.xz -C /usr/src/ruby --strip-components=1; \
	rm ruby.tar.xz; \
	\
	cd /usr/src/ruby; \
	\
# hack in "ENABLE_PATH_CHECK" disabling to suppress:
#   warning: Insecure world writable dir
	{ \
		echo '#define ENABLE_PATH_CHECK 0'; \
		echo; \
		cat file.c; \
	} > file.c.new; \
	mv file.c.new file.c; \
	\
	./configure \
		--disable-install-doc \
		--enable-shared \
	; \
	make -j "$(nproc)"; \
	make install; \
	\
	cd /; \
	rm -r /usr/src/ruby; \
# make sure bundled "rubygems" is older than RUBYGEMS_VERSION (https://github.com/docker-library/ruby/issues/246)
	ruby -e 'exit(Gem::Version.create(ENV["RUBYGEMS_VERSION"]) > Gem::Version.create(Gem::VERSION))'; \
	gem update --system "$RUBYGEMS_VERSION" && rm -r /root/.gem/; \
# rough smoke test
	ruby --version; \
	gem --version; \
	bundle --version

# install things globally, for great justice
# and don't create ".bundle" in all our apps
ENV GEM_HOME /usr/local/bundle
ENV BUNDLE_PATH="$GEM_HOME" \
	BUNDLE_SILENCE_ROOT_WARNING=1 \
	BUNDLE_APP_CONFIG="$GEM_HOME"
# path recommendation: https://github.com/bundler/bundler/pull/6469#issuecomment-383235438
ENV PATH $GEM_HOME/bin:$BUNDLE_PATH/gems/bin:$PATH
# adjust permissions of a few directories for running "gem install" as an arbitrary user
RUN mkdir -p "$GEM_HOME" && chmod 777 "$GEM_HOME"
# (BUNDLE_PATH = GEM_HOME, no need to mkdir/chown both)

# END CONTENT FROM OFFICIAL RUBY DOCKERFILE

ENV COSMOS_VERSION 4.4.0

RUN --mount=type=secret,id=sslsecret SSL_CERT_FILE=/run/secrets/sslsecret gem install rake --no-document
RUN --mount=type=secret,id=sslsecret SSL_CERT_FILE=/run/secrets/sslsecret gem install cosmos -v $COSMOS_VERSION s--no-document
RUN cosmos demo /cosmos

WORKDIR /cosmos
# This works around an issue with setpgrp if you already have it in this environment
CMD true && ruby Launcher