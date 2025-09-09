# ----------- Stage 1: Builder -----------
FROM maven:3.9.9-eclipse-temurin-24 AS builder

WORKDIR /app

# Copy pom.xml and download dependencies first (to leverage cache)
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copy source code and build
COPY src ./src
RUN mvn clean package -DskipTests

# ----------- Stage 2: Runner -----------
FROM eclipse-temurin:24-jre-alpine AS runner

WORKDIR /app

# Set environment variables
ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75"
ENV PORT=8080
EXPOSE 8080

# Copy only the jar from builder
COPY --from=builder /app/target/*.jar app.jar

# Run as non-root user
RUN addgroup -S spring && adduser -S spring -G spring
USER spring

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
