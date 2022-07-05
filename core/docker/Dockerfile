#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
FROM registry.access.redhat.com/ubi8/ubi

ENV JAVA_HOME /usr/lib/jvm/zulu11
ENV TRINO_HOME=/usr/local/trino

RUN set -xeu
RUN yum -y -q install https://cdn.azul.com/zulu/bin/zulu-repo-1.0.0-1.noarch.rpm
RUN yum -y -q install python3 zulu11-jdk less
RUN yum -q clean all
RUN yum -y -q install python3-pip
RUN pip3 install jinja2

RUN rm -rf /var/cache/yum
RUN alternatives --set python /usr/bin/python3
RUN groupadd trino --gid 1000
RUN useradd trino --uid 1000 --gid 1000
RUN mkdir -p /usr/lib/trino /data/trino
RUN chown -R "trino:trino" /usr/lib/trino /data/trino
RUN chown -R "trino:trino" /etc
RUN chown -R "trino:trino" /var

ARG TRINO_VERSION
COPY trino-cli-${TRINO_VERSION}-executable.jar /usr/bin/trino
COPY --chown=trino:trino trino-server-${TRINO_VERSION} /usr/local/trino
# COPY --chown=trino:trino default/etc /etc/trino

### CUSTOM STUFF ###
COPY --chown=trino:trino scripts ./usr/local/trino/scripts

USER trino:trino
ENV LANG en_US.UTF-8

CMD ["/usr/lib/trino/bin/run-trino"]
HEALTHCHECK --interval=10s --timeout=5s --start-period=10s \
  CMD /usr/lib/trino/bin/health-check
