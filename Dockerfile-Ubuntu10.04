# Use Ubuntu 10 as the base image
FROM ubuntu:10.04

ARG OLD_LIBRARY_PATH

# Set the working directory inside the container
WORKDIR /app

# Update sources.list to use old-releases.ubuntu.com
RUN sed -i 's/archive.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list

# Install necessary dependencies
RUN apt-get update && apt-get install -y \
    git-core \
    ruby \
    ruby-dev \
    rdoc \
    build-essential \
    curl \
    && apt-get clean

# Download, build, and install OpenSSL 1.0.1 from source
RUN curl -o /app/open-ssl.tar.gz http://github.com/openssl/openssl/releases/download/OpenSSL_1_0_1u/openssl-1.0.1u.tar.gz
RUN ls -a
RUN tar -xzf openssl-1.0.1u.tar.gz
RUN cd openssl-1.0.1 && \
    ./config --prefix=/usr/local/openssl-1.0.1u shared zlib
RUN cd openssl-1.0.1 && \
    make
RUN cd openssl-1.0.1 && \
    make install
RUN rm -rf openssl-1.0.1 openssl-1.0.1u.tar.gz

# Copy the source code into the container
COPY . /app

# Download and install RubyGems 1.8.5 from source and add it to the PATH
RUN curl -O http://production.cf.rubygems.org/rubygems/rubygems-1.8.5.tgz && \
    tar -xzf rubygems-1.8.5.tgz && \
    cd rubygems-1.8.5 && \
    ruby setup.rb && \
    cd .. && \
    rm -rf rubygems-1.8.5 rubygems-1.8.5.tgz && \
    echo 'export PATH=$PATH:/usr/bin/gem1.8' >> ~/.bashrc \
    . ~/.bashrc

ENV PATH="$PATH:/usr/bin/gem1.8"

RUN ruby -ropenssl -e "p OpenSSL::OPENSSL_VERSION"

RUN curl -V
# RUN curl -L 'https://git.io/rg-ssl' | ruby

# Install Ruby dependencies from the Gemfile
RUN gem1.8 install bundler -v '1.1.0'
RUN bundle install

# Default command
CMD ["ruby", "app.rb"]