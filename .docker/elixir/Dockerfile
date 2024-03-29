FROM erlang:22-slim

# elixir expects utf8.
ENV ELIXIR_VERSION="v1.9.4" \
	LANG=C.UTF-8

ENV NODE_VERSION 11.x

RUN set -xe \
	&& ELIXIR_DOWNLOAD_URL="https://github.com/elixir-lang/elixir/archive/${ELIXIR_VERSION}.tar.gz" \
	&& ELIXIR_DOWNLOAD_SHA256="f3465d8a8e386f3e74831bf9594ee39e6dfde6aa430fe9260844cfe46aa10139" \
	&& buildDeps=' \
		ca-certificates \
		make \
    locales \
    nodejs \
	' \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends curl gnupg sudo \
  && curl -sL https://deb.nodesource.com/setup_${NODE_VERSION} | sudo -E bash - \
  && apt-get install -y --no-install-recommends $buildDeps \
  && locale-gen ja_JP.utf8 \
  && npm install npm@latest -g \
	&& curl -fSL -o elixir-src.tar.gz $ELIXIR_DOWNLOAD_URL \
	&& echo "$ELIXIR_DOWNLOAD_SHA256  elixir-src.tar.gz" | sha256sum -c - \
	&& mkdir -p /usr/local/src/elixir \
	&& tar -xzC /usr/local/src/elixir --strip-components=1 -f elixir-src.tar.gz \
	&& rm elixir-src.tar.gz \
	&& cd /usr/local/src/elixir \
	&& make install clean \
	&& apt-get purge -y --auto-remove $buildDeps \
	&& rm -rf /var/lib/apt/lists/* \
  && npm install npm@latest -g

# Set the locale
ENV ENV LANG='ja_JP.utf8' LANGUAGE='ja_JP.utf8' LC_ALL='ja_JP.utf8'

# Set the timezone
RUN echo "${TZ}" > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata

# Install Hex+Rebar
RUN mix local.hex --force && \
    mix archive.install hex phx_new 1.4.11 --force && \
    mix local.rebar --force
