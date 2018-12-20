# Copyright 2016 The Kubernetes Authors.
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

FROM s390x/golang:GO_VERSION

# Install dep as root
ENV DEP_VERSION=v0.5.0
RUN go get -u -v github.com/golang/dep/cmd/dep && \
    ln -s /go/bin/dep /usr/local/bin/dep

# Install etcd
ENV ETCD_DATA_DIR=/data ETCD_UNSUPPORTED_ARCH=s390x
RUN mkdir -p /src/github.com/coreos && \
    cd /src/github.com/coreos && \
    rm -rf etcd && \
    git clone https://github.com/coreos/etcd && \
    cd etcd && \
    git checkout v3.2.24 && \
    mkdir -p /data && \
    ./build && \
    cd bin && cp -rf etcd etcdctl /usr/bin/

# Install the golint, use this to check our source for niceness
COPY vendor/golang.org /go/src/golang.org
RUN go install golang.org/x/lint/golint

# Install the href checker for our md files, update PATH to include it
RUN git clone https://github.com/duglin/vlinker.git /vlinker
ENV PATH=$PATH:/vlinker/bin

# Install the azure client, used to publish svcat binaries
ENV AZCLI_VERSION=v0.3.1
RUN go get github.com/carolynvs/az-cli && \
    cd $GOPATH/src/github.com/carolynvs/az-cli/ && \
    git checkout $AZCLI_VERSION && \
    make build && \
    cp ./bin/az /usr/local/bin/az && \
    chmod +x /usr/local/bin/az

# Create the full dir tree that we'll mount our src into when we run the image
RUN mkdir -p /go/src/github.com/kubernetes-incubator/service-catalog

# Default to our src dir
WORKDIR /go/src/github.com/kubernetes-incubator/service-catalog
