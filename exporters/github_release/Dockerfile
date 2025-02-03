FROM alpine/flake8:latest as linter
WORKDIR /apps/
COPY . /apps/
## ingore E501 line too long (XX > 79 characters)
RUN flake8 --ignore="E501" *.py

FROM --platform=linux/amd64 python:3.11-slim-buster

WORKDIR /apps/

ARG USER=exporter
ARG UID=10005

# See https://stackoverflow.com/a/55757473/12429735RUN
RUN adduser \
    --disabled-password \
    --gecos "" \
    --shell "/sbin/nologin" \
    --uid "${UID}" \
    "${USER}"

RUN apt-get update && apt-get install -y gcc g++
COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt --no-cache-dir

COPY --from=linter /apps/exporter.py app.py
COPY --from=linter /apps/projects.yml projects.yml

RUN chown -R ${USER}:${USER} /apps

USER ${USER}

CMD ["python3", "app.py"]
