FROM python:3.6-slim

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

ENV PYTHONPATH /home/lisa
ENV FLASK_APP /home/lisa/lisa/web_api/app.py
WORKDIR /home/lisa

RUN useradd -m lisa
COPY --chown=lisa:lisa ./requirements.txt /home/lisa/requirements.txt

RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    g++ \
    libpcap-dev \
    && pip install -r requirements.txt \
    && mkdir -p data/storage data/geolite2databases \
    && touch data/storage/ipblacklist \
    && touch data/geolite2databases/GeoLite2-ASN.mmdb \
    && touch data/geolite2databases/GeoLite2-City.mmdb \
    && rm -rf /var/lib/apt/lists/*

USER lisa
COPY --chown=lisa:lisa ./docker /home/lisa/docker
COPY --chown=lisa:lisa ./lisa /home/lisa/lisa

EXPOSE 5000

CMD ["uwsgi", "--ini", "docker/api/app.ini"]
