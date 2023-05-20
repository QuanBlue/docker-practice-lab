FROM ubuntu

LABEL maintainer="QuanBlue" \
      maintainer.email="quannguyenthanh558@gmail.com"

RUN apt update && apt upgrade -y
RUN apt install python3 -y

WORKDIR /home

ADD hello.py ./test/

ENTRYPOINT ["python3"]
CMD ["/home/test/hello.py"]