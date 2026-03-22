FROM mysql:8.0.33

LABEL project="multi-tier"
LABEL author="Tawfeeq"
ENV MYSQL_ROOT_PASSWORD=vprodbpass
ENV MYSQL_DATABASE=accounts

COPY db_backup.sql /docker-entrypoint-initdb.d/

RUN chmod 644 /docker-entrypoint-initdb.d/db_backup.sql

EXPOSE 3306

HEALTHCHECK --interval=30s --timeout=5s --start-period=30s \
  CMD mysqladmin ping -h localhost -pvprodbpass || exit 1
