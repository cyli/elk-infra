# Logstash
#
# logstash is a tool for managing events and logs
#
# VERSION               1.4.2

FROM dockerfile/java

# Install logstash
RUN \
  cd /tmp && \
  wget https://download.elasticsearch.org/logstash/logstash/logstash-1.4.2.tar.gz && \
  tar xvzf logstash-1.4.2.tar.gz && \
  rm -f logstash-1.4.2.tar.gz && \
  mv /tmp/logstash-1.4.2 /logstash

ADD syslog.conf /logstash/default.syslog.conf

EXPOSE 5000

CMD /logstash/bin/logstash -f /logstash/default.syslog.conf
