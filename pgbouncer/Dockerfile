ARG PGBOUNCER_VERSION=1.25.2

FROM debian:trixie AS build

ARG PGBOUNCER_VERSION

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    pkg-config \
    libevent-dev \
    libssl-dev \
    libc-ares-dev \
    libpam0g-dev \
    libldap2-dev \
    curl \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL "https://www.pgbouncer.org/downloads/files/${PGBOUNCER_VERSION}/pgbouncer-${PGBOUNCER_VERSION}.tar.gz" \
    | tar xz -C /tmp

RUN cd /tmp/pgbouncer-${PGBOUNCER_VERSION} \
    && ./configure --prefix=/usr/local --with-cares --with-pam --with-ldap \
    && make -j"$(nproc)" pgbouncer \
    && install -m 755 pgbouncer /usr/local/bin/pgbouncer

FROM debian:trixie-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    libevent-2.1-7t64 \
    libssl3t64 \
    libc-ares2 \
    libpam0g \
    libldap2 \
    && rm -rf /var/lib/apt/lists/*

RUN groupadd -r pgbouncer && useradd -r -g pgbouncer -u 1000 pgbouncer \
    && mkdir -p /etc/pgbouncer \
    && chown pgbouncer:pgbouncer /etc/pgbouncer

COPY --from=build /usr/local/bin/pgbouncer /usr/local/bin/pgbouncer

USER 1000
EXPOSE 6432

ENTRYPOINT ["pgbouncer"]
CMD ["/etc/pgbouncer/pgbouncer.ini"]
