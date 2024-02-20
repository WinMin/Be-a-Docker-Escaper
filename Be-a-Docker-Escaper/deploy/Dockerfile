FROM ubuntu:20.04

RUN sed -i "s/http:\/\/archive.ubuntu.com/http:\/\/mirrors.aliyun.com/g" /etc/apt/sources.list && \
         sed -i "s/http:\/\/security.ubuntu.com/http:\/\/mirrors.aliyun.com/g" /etc/apt/sources.list

RUN  apt-get update && \
        apt-get -y dist-upgrade

RUN DEBIAN_FRONTEND="noninteractive" apt-get -y install tzdata ca-certificates wget qemu-system cloud-image-utils python3

RUN mkdir /home/chall

WORKDIR /home/chall

RUN wget http://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64.img
	
COPY ./user-data /home/chall/
COPY ./check.py /home/chall/

RUN cloud-localds user-data.img user-data && \
    qemu-img resize focal-server-cloudimg-amd64.img +20G

#apply cloud-init

RUN qemu-system-x86_64 \
  -drive "file=focal-server-cloudimg-amd64.img,format=qcow2" \
  -drive "file=user-data.img,format=raw" \
  -device rtl8139,netdev=net0 \
  -m 8G \
  -netdev user,id=net0 \
  -smp 4 \
  -nographic 

EXPOSE 5555

CMD qemu-system-x86_64 \
  -drive "file=focal-server-cloudimg-amd64.img,format=qcow2" \
  -device rtl8139,netdev=net0 \
  -m 1G \
  -netdev user,id=net0,hostfwd=tcp::5555-:22 \
  -smp 2 \
  -nographic 
