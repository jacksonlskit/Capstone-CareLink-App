# ==============================================================================
# Stage 1: Build Microservice via Maven Reactor
# ==============================================================================
FROM maven:3.8.8-eclipse-temurin-17 AS builder
WORKDIR /build

# Copy root POM and all service modules to satisfy Maven reactor structure
COPY pom.xml ./
COPY services ./services

# Build only customers-service and its dependencies
RUN mvn -pl services/customers-service -am clean package -DskipTests

# ==============================================================================
# Stage 2: Hardened, Unprivileged Runtime Environment
# ==============================================================================
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Upgrade Alpine base packages to resolve known OS CVEs (including brotli-libs)
# and create unprivileged non-root user
RUN apk upgrade --no-cache && \
    addgroup -S appgroup && \
    adduser -S appuser -G appgroup

# Copy compiled executable JAR from builder stage
COPY --from=builder /build/services/customers-service/target/*.jar app.jar

USER appuser:appgroup

EXPOSE 8081

ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "app.jar"]
