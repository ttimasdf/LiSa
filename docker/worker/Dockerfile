FROM python:3.6-slim

ARG maxmind_key

RUN printf '# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释\n\
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free\n\
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free\n\
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free\n\
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free\n\
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free\n\
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free\n\
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free\n\
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free\n\
' > /etc/apt/sources.list
ARG http_proxy
ARG https_proxy

RUN useradd -m lisa
ENV PYTHONPATH /home/lisa
WORKDIR /home/lisa

COPY --chown=lisa:lisa ./requirements.txt /home/lisa/requirements.txt

RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    g++ \
    libpcap-dev \
    make \
    patch \
    git \
    qemu \
    qemu-system \
    openvpn \
    binutils \
    iprange \
    wget \
    tar \
    e2tools \
    && git config --global http.proxyAuthMethod basic \
    && git clone https://github.com/radare/radare2 --depth=1 \
    && radare2/sys/install.sh \
    && echo "Downloading LiSa Linux images ..." \
    && wget https://github.com/danieluhricek/linux-images/archive/v1.0.1.tar.gz -q -O - | tar xz -C /home/lisa \
    && mv /home/lisa/linux-images-1.0.1 /home/lisa/images \
    && pip install -r requirements.txt \
    && pip install flower \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /radare2/.git

COPY --chown=lisa:lisa ./docker /home/lisa/docker
COPY --chown=lisa:lisa ./lisa /home/lisa/lisa

RUN ./docker/worker/maxmind.sh $maxmind_key

CMD ["./docker/worker/init.sh"]
