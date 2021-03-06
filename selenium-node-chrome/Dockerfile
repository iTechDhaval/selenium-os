FROM selenium/node-chrome:3.12.0

ENV HOME=/home/seluser

USER root

ADD http://icertprod.iad.ca.inet/certenroll/iRoot2015CA.crt \
    http://icertprod.iad.ca.inet/certenroll/iSub2015CA.crt \
    /tmp/

RUN apt-get update && apt-get -qqy install openssl ca-certificates libnss3-tools curl && \
    mkdir /usr/local/share/ca-certificates/cacert.org && \
    openssl x509 \
        -inform der \
        -in /tmp/iRoot2015CA.crt \
        -out /usr/local/share/ca-certificates/cacert.org/iRoot2015CA.pem && \
    openssl x509 \
        -inform der \
        -in /tmp/iSub2015CA.crt \
        -out /usr/local/share/ca-certificates/cacert.org/iSub2015CA.pem && \
    cd /usr/local/share/ca-certificates/cacert.org && \
    find /usr/local/share/ca-certificates/cacert.org/ -name "*.pem" -exec sh -c 'mv "$1" "${1%.pem}.crt"' _ {} \; && \
    update-ca-certificates && \
    chmod 0655 /tmp/*.crt && \
    apt-get -qyy autoremove && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

COPY ./contrib /usr/local/bin

RUN mkdir -p $HOME/.pki/nssdb && \
    chmod -R 700 $HOME/.pki/nssdb && \
    ls -la $HOME/.pki/nssdb

RUN certutil -d $HOME/.pki/nssdb -N && \
    ls -la $HOME/.pki/nssdb && \
    chmod -R 777 $HOME/.pki/nssdb && \
    ls -la /tmp/*.crt && \
    certutil -d sql:$HOME/.pki/nssdb -A -t TC -n "iRoot2015CA.crt" -i /tmp/iRoot2015CA.crt && \
    certutil -d sql:$HOME/.pki/nssdb -A -t TC -n "iSub2015CA.crt" -i /tmp/iSub2015CA.crt

RUN chown -R 1001:0 /opt/selenium && \
  /usr/local/bin/fix-permissions /opt/selenium && \
  /usr/local/bin/fix-permissions $HOME

USER seluser

WORKDIR $HOME

