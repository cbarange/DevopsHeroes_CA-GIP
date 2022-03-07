FROM golang:latest

#docker build -t $REGISTRY_IMAGE:$CI_COMMIT_SHA .
#docker build -t hackaton-api:$CI_COMMIT_SHA .


#ARG TARGETARCH TARGETOS

#RUN echo "I am running on $TARGETARCH building for $TARGETPLATFORM" > /log

ENV HACATHON_REWARD_TAG="v0.1.1"
# v0.1.1

WORKDIR /root/
RUN ln -s /usr/bin/dpkg-split /usr/sbin/dpkg-split
RUN ln -s /usr/bin/dpkg-deb /usr/sbin/dpkg-deb
RUN ln -s /bin/rm /usr/sbin/rm
RUN ln -s /bin/tar /usr/sbin/tar

COPY . .
RUN go mod vendor
RUN GO111MODULE="on" CGO_ENABLED=0 go build  -a -ldflags '-extldflags "-static"' -v -o ./build/hackathon-api ./main.go

RUN apt update && \
      apt install -y --no-install-recommends \
      xvfb libfontconfig wget fontconfig xfonts-75dpi xfonts-100dpi xfonts-scalable xfonts-base \
      && rm -rf /var/lib/apt/lists/*


RUN wget  https://github.com/ca-gip/hackathon-api/releases/download/${HACATHON_REWARD_TAG}/hackathon-reward-linux-amd64 -O hackathon-reward
RUN chmod a+x hackathon-reward && \
    mv ./hackathon-reward /usr/local/bin/hackathon-reward

RUN wget https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox_0.12.6-1.buster_amd64.deb
RUN apt update && \
    apt install ./wkhtmltox_0.12.6-1.buster_amd64.deb  -y && \
    rm wkhtmltox_0.12.6-1.buster_amd64.deb


EXPOSE 8080
CMD ["./build/hackathon-api"]
