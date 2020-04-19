FROM postgres:latest

WORKDIR /

ENV POSTGRES_PASSWORD postgres
ENV POSTGRES_USER postgres

RUN  mkdir -p /docker-entrypoint-initdb.d

COPY pg-init-scripts/* /docker-entrypoint-initdb.d/