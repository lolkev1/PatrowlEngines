FROM alpine:3.16.3
LABEL Name="SSLScan\ \(Patrowl engine\)" Version="1.4.32"

ENV CFLAGS "-D__USE_GNU"

# Set the working directory
RUN mkdir -p /opt/patrowl-engines/sslscan
RUN mkdir -p /opt/patrowl-engines/sslscan/results
# RUN mkdir -p /opt/patrowl-engines/sslscan/external-libs
WORKDIR /opt/patrowl-engines/sslscan

# Copy the current directory contents into the container at /
COPY __init__.py .
COPY engine-sslscan.py .
COPY sslscan.json.sample sslscan.json
COPY requirements.txt .
COPY README.md .
COPY VERSION .

WORKDIR /opt/patrowl-engines/sslscan/external-libs
RUN apk add --update --no-cache \
      coreutils \
      build-base \
      linux-headers \
      git \
      perl \
      zlib-dev \
      libc6-compat \
      openssl \
      python3 \
      python3-dev \
      py3-pip && \
    git clone https://github.com/rbsec/sslscan.git && \
    cd sslscan && \
    make static && \
    rm -rf /var/cache/apk/*

#WORKDIR /opt/patrowl-engines/sslscan/libs
#RUN apk add --no-cache curl bash procps drill git coreutils
#RUN rm -rf /var/cache/apk/* /tmp/*
#RUN git clone --depth=1 https://github.com/drwetter/testssl.sh.git .


WORKDIR /opt/patrowl-engines/sslscan
RUN pip3 install --upgrade pip
RUN pip3 install --trusted-host pypi.python.org -r requirements.txt --ignore-installed

# TCP port exposed by the container (NAT)
EXPOSE 5014

# Run app.py when the container launches
CMD ["gunicorn", "engine-sslscan:app", "-b", "0.0.0.0:5014", "--access-logfile", "-"]
