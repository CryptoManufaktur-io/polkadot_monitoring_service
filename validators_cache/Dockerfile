FROM --platform=linux/amd64 ubuntu:22.04

ARG USER=exporter
ARG UID=10005

WORKDIR /apps

# See https://stackoverflow.com/a/55757473/12429735RUN
RUN adduser \
    --disabled-password \
    --gecos "" \
    --shell "/sbin/nologin" \
    --uid "${UID}" \
    "${USER}"

COPY requirements.txt /apps/requirements.txt
COPY update_cache.py /apps/update_cache.py
COPY validators.txt /apps/validators.txt

RUN apt update && apt install -y gcc g++ libpq-dev git python3 python3-pip
RUN pip3 install -r requirements.txt

RUN chown -R ${USER}:${USER} /apps

USER ${USER}

CMD ["python3", "update_cache.py"]
