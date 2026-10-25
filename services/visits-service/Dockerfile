# Stage 1: Build the artifact
FROM maven:3.9.6-eclipse-temurin-17-alpine AS builder
WORKDIR /build

# Copy root pom and module poms for dependency caching
COPY pom.xml ./pom.xml
COPY services/visits-service/pom.xml ./services/visits-service/pom.xml
COPY services/customers-service/pom.xml ./services/customers-service/pom.xml
COPY services/vets-service/pom.xml ./services/vets-service/pom.xml
COPY services/visits-service/pom.xml ./services/visits-service/pom.xml

# Copy service source code
COPY services/visits-service/src ./services/visits-service/src

# Package target microservice
RUN mvn -pl services/visits-service -am clean package -DskipTests

# Stage 2: Hardened runtime
FROM eclipse-temurin:17-jre-alpine AS runner
WORKDIR /app

# Run as non-root user (Security Gate Requirement)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

COPY --from=builder --chown=appuser:appgroup /build/services/visits-service/target/*.jar app.jar

EXPOSE 8080
ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "app.jar"]
