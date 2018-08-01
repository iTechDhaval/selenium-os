FROM selenium/node-firefox:3.12.0

ENV HOME=/home/seluser

USER root

ADD http://icertprod.iad.ca.inet/certenroll/iRoot2015CA.crt \
    http://icertprod.iad.ca.inet/certenroll/iSub2015CA.crt \
    /tmp/

RUN apt-get update && \
    apt-get -qqy install openssl ca-certificates libnss3-tools && \
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
    chmod 0605 /tmp/*.crt && \
    apt -qyy autoremove && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/* && \
    apt-get -qyy clean

USER seluser

RUN nohup sh -c "firefox --headless </dev/null &>/dev/null &" && sleep 1 && kill -9 $(ps ax | grep firefox | grep -v grep | grep -v webdriver | awk '{print $1}') && \
    certutil -d sql:$HOME/.mozilla/firefox/$(ls $HOME/.mozilla/firefox/ | grep default) -N && \
    ls -la $HOME/.mozilla/firefox/$(ls $HOME/.mozilla/firefox/ | grep default) && \
    certutil -d sql:$HOME/.mozilla/firefox/$(ls $HOME/.mozilla/firefox/ | grep default) -A -t TC -n "iRoot2015CA.crt" -i /tmp/iRoot2015CA.crt && \
    certutil -d sql:$HOME/.mozilla/firefox/$(ls $HOME/.mozilla/firefox/ | grep default) -A -t TC -n "iSub2015CA.crt" -i /tmp/iSub2015CA.crt

COPY ./contrib /usr/local/bin

RUN chown -R 1001:0 /opt/selenium && \
  /usr/local/bin/fix-permissions /opt/selenium && \
  /usr/local/bin/fix-permissions $HOME

USER 1001
WORKDIR $HOME
