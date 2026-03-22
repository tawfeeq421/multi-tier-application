FROM maven:3.9.9-eclipse-temurin-11 AS builder

WORKDIR /app

RUN git clone https://github.com/tawfeeq421/multi-tier-application.git

# ✅ Correct path
WORKDIR /app/multi-tier-application

RUN mvn clean package -DskipTests

FROM tomcat:9.0-jre11-temurin

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=builder /app/multi-tier-application/target/*.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080

CMD ["catalina.sh", "run"]
