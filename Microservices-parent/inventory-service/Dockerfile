FROM maven:3.8.1-openjdk-17-slim as build
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline

# Copy the rest of the source code and build the application
COPY src ./src
RUN mvn package -DskipTests


FROM eclipse-temurin:21-jre-jammy as production
WORKDIR /
EXPOSE 8080
COPY --from=build /app/target/inventory-service-*.jar /inventory-service.jar

# Copy OTEL agent from a tiny fetcher stage
ADD https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar /opt/opentelemetry/opentelemetry-javaagent.jar
ENV JAVA_TOOL_OPTIONS="-javaagent:/opt/opentelemetry/opentelemetry-javaagent.jar"

# Run the app
ENTRYPOINT ["java","-jar","/inventory-service.jar"]
