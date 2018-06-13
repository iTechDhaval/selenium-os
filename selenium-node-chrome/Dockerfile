FROM selenium/node-chrome:3.12.0

ENV HOME=/home/seluser

USER root

ADD http://icertprod.iad.ca.inet/certenroll/iRoot2015CA.crt \
    http://icertprod.iad.ca.inet/certenroll/iSub2015CA.crt \
    /tmp/

RUN apt-get -qqy install curl openssl ca-certificates && \
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
    rename \
        's/.pem$/.crt/' \
        /usr/local/share/ca-certificates/cacert.org/*.pem && \
    update-ca-certificates && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

COPY ./contrib /usr/local/bin

RUN chown -R 1001:0 /opt/selenium && \
  /usr/local/bin/fix-permissions /opt/selenium && \
  /usr/local/bin/fix-permissions $HOME

USER 1001
WORKDIR $HOME
