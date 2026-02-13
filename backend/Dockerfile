# ---- build stage ----
    FROM maven:3.9-eclipse-temurin-8 AS build
    WORKDIR /app
    
    COPY pom.xml .
    RUN mvn -B -q -DskipTests dependency:go-offline
    
    COPY src ./src
    RUN mvn -B -DskipTests package
    
    # ---- runtime stage ----
    FROM eclipse-temurin:8-jre
    
    RUN useradd -r -u 10001 -g root appuser
    
    WORKDIR /app
    COPY --from=build /app/target/*.jar /app/app.jar
    
    # Java often needs /tmp writable even with read-only root FS
    RUN mkdir -p /tmp && chown -R 10001:0 /tmp
    
    USER 10001
    EXPOSE 8080
    ENV JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75.0 -Djava.security.egd=file:/dev/./urandom"
    
    ENTRYPOINT ["java","-jar","/app/app.jar"]
    