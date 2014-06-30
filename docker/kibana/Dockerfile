# Kibana
#
# Kibana is a javascript app that displays elasticsearch data, and that
# needs nginx to serve up
#
# VERSION               3.1.0

FROM dockerfile/nginx

# Download kibana
RUN \
  cd /tmp && \
  wget https://download.elasticsearch.org/kibana/kibana/kibana-3.1.0.tar.gz && \
  tar xvzf kibana-3.1.0.tar.gz && \
  rm -f kibana-3.1.0.tar.gz && \
  mv /tmp/kibana-3.1.0 /kibana3

RUN sed -i -e 's|"+window.location.hostname.*$|localhost:8123",|' /kibana3/config.js
ADD nginx.conf /etc/nginx/nginx.conf
