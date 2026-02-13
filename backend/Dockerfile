# ---- build stage ----
    FROM maven:3.9-eclipse-temurin-8 AS build
    WORKDIR /app
    
    # Cache deps
    COPY pom.xml .
    RUN mvn -B -q -DskipTests dependency:go-offline
    
    # Build
    COPY src ./src
    RUN mvn -B -DskipTests package
    
    # ---- runtime stage (distroless, nonroot) ----
    # Distroless reduces OS package surface area => typically far fewer CVEs
    FROM gcr.io/distroless/java8-debian11:nonroot
    
    WORKDIR /app
    COPY --from=build /app/target/*.jar /app/app.jar
    
    # Expose app port
    EXPOSE 8080
    
    # Container-friendly JVM flags
    ENV JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75.0 -Djava.security.egd=file:/dev/./urandom"
    
    ENTRYPOINT ["java","-jar","/app/app.jar"]
    