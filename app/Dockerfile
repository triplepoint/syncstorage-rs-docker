FROM rust:latest

# Create needed directories
RUN mkdir /app /config
WORKDIR /app

# Install needed packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3-virtualenv \
    python3-pip \
    python3-dev \
    default-mysql-client \
    && rm -rf /var/lib/apt/lists/*

# Clone syncstorage-rs and build it
ARG SYNCSTORAGE_RS_VERSION=0.18.3
RUN git clone https://github.com/mozilla-services/syncstorage-rs ./ \
    && git checkout $SYNCSTORAGE_RS_VERSION \
    && cargo install --path ./syncserver --no-default-features --features=syncstorage-db/mysql --locked \
    && cargo install diesel_cli --no-default-features --features 'mysql' \
    && cargo clean

# Setup the Python venv
RUN virtualenv venv \
    && /app/venv/bin/pip install -r requirements.txt \
    && /app/venv/bin/pip install -r tools/tokenserver/requirements.txt \
    && /app/venv/bin/pip install pyopenssl==25.1.0

# Cleanup
RUN rm -rf \
    /var/cache \
    /var/log \
    /var/lib/apt \
    /var/lib/dpkg

# Copy entrypoint script and set it
COPY --chmod=755 entrypoint.sh /
ENTRYPOINT /entrypoint.sh
