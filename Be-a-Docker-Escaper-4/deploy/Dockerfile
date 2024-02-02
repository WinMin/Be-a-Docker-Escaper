FROM ubuntu:20.04

RUN sed -i "s/http:\/\/archive.ubuntu.com/http:\/\/mirrors.aliyun.com/g" /etc/apt/sources.list && \
         sed -i "s/http:\/\/security.ubuntu.com/http:\/\/mirrors.aliyun.com/g" /etc/apt/sources.list

RUN  apt-get update && \
        apt-get -y dist-upgrade

RUN DEBIAN_FRONTEND="noninteractive" apt-get -y install tzdata ca-certificates wget qemu-system cloud-image-utils python3
RUN mkdir /home/chall

WORKDIR /home/chall

RUN wget https://cloud-images.ubuntu.com/releases/22.04/release-20221214/ubuntu-22.04-server-cloudimg-amd64.img
COPY ./user-data /home/chall/
RUN cloud-localds user-data.img user-data && \
    qemu-img resize ubuntu-22.04-server-cloudimg-amd64.img +20G

#apply cloud-init
RUN qemu-system-x86_64 \
  -drive "file=ubuntu-22.04-server-cloudimg-amd64.img,format=qcow2" \
  -drive "file=user-data.img,format=raw" \
  -nic user,model=virtio \
  -m 4G \
  -smp 4 \
  -nographic

EXPOSE 5555

CMD qemu-system-x86_64 \
  -drive "file=ubuntu-22.04-server-cloudimg-amd64.img,format=qcow2" \
  -m 1G \
  -nic user,model=virtio,hostfwd=tcp:0.0.0.0:5555-:22 \
  -smp 2 \
  -nographic \
  --enable-kvm \
  -monitor null