FROM python:3.8-slim

WORKDIR /ryu

RUN apt-get update && apt-get install -y git

RUN git clone https://github.com/faucetsdn/ryu.git

RUN pip install ./ryu

EXPOSE 6633/tcp
EXPOSE 8080/tcp

CMD ["ryu-manager", "ryu.app.ofctl_rest", "ryu.app.simple_switch_13", "--ofp-tcp-listen-port", "6633", "--wsapi-host", "0.0.0.0", "--wsapi-port", "8080"]