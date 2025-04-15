FROM maven:3.9.9-eclipse-temurin-21 AS build

# Set working directory
WORKDIR /app

# Copy pom.xml separately to leverage Docker cache
COPY pom.xml .
# Download dependencies (will be cached if pom.xml doesn't change)
RUN mvn dependency:go-offline

# Copy source code
COPY src ./src

# Build the application
RUN mvn clean package -DskipTests

# Runtime stage
FROM eclipse-temurin:21-jre-alpine

# Create a non-root user
RUN addgroup -S firewatch && adduser -S firewatch -G firewatch

# Set working directory
WORKDIR /app

# Copy JAR from build stage
COPY --from=build /app/target/FireWatch03-0.0.1-SNAPSHOT.jar app.jar

# Switch to non-root user
USER firewatch

# Expose application port
EXPOSE 8080

# Add health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget -q --spider http://localhost:8080/actuator/health || exit 1

# Start the application
ENTRYPOINT ["java", "-jar", "app.jar"]