FROM alpine/flake8:latest as linter
WORKDIR /apps/
COPY . /apps/
## ingore E501 line too long (XX > 79 characters)
RUN flake8 --ignore="E501" *.py

FROM --platform=linux/amd64 python:3.11-slim-buster 

ARG exporter
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

RUN apt-get update && apt-get install -y gcc g++
COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt --no-cache-dir

COPY --from=linter /apps/${exporter}.py app.py
COPY --from=linter /apps/functions.py functions.py

# Belt and suspenders
RUN chown -R ${USER}:${USER} /apps
RUN chmod -R 755 /usr/local/bin/*

USER ${USER}
CMD ["python3", "app.py"]
