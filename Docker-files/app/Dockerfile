FROM maven:3.9.9-eclipse-temurin-11 AS builder
RUN git clone https://github.com/tawfeeq421/multi-tier-application.git
WORKDIR /app/multi-tier
RUN mvn clean package -DskipTests

FROM tomcat:9.0-jre11-temurin
LABEL project="Java Web Application"
LABEL author="Tawfeeq"
RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=builder /app/vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=5s --start-period=30s \
  CMD curl -f http://localhost:8080/ || exit 1
CMD ["catalina.sh", "run"]

