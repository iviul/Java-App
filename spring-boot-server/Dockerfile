# Build stage
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B
COPY src/ ./src
RUN mvn -q clean package -DskipTests 

# Runtime stage
FROM eclipse-temurin:17-jre
RUN groupadd -r app && useradd -r -g app app
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
USER app
EXPOSE 8081

HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget -qO- http://localhost:8081/actuator/health || exit 1

# Run 
ENTRYPOINT ["java", "-jar", "app.jar"]
