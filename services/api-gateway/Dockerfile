# ==============================================================================
# Stage 1: Build Microservice via Maven Reactor
# ==============================================================================
FROM maven:3.8.8-eclipse-temurin-17 AS builder
WORKDIR /build

# Copy root POM and all service modules to satisfy Maven reactor structure
COPY pom.xml ./
COPY services ./services

# Build only api-gateway and its dependencies
RUN mvn -pl services/api-gateway -am clean package -DskipTests

# ==============================================================================
# Stage 2: Hardened, Unprivileged Runtime Environment
# ==============================================================================
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# DevSecOps Enforcement: Run as unprivileged non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy compiled executable JAR from builder stage
COPY --from=builder /build/services/api-gateway/target/*.jar app.jar

USER appuser:appgroup

EXPOSE 8080

ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "app.jar"]
