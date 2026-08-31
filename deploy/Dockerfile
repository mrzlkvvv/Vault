FROM bellsoft/liberica-openjdk-alpine:25 AS builder
WORKDIR /build

# Cache dependencies
COPY gradlew settings.gradle.kts build.gradle.kts ./
COPY gradle ./gradle
RUN ./gradlew dependencies --no-daemon

# Build the application jar
COPY src ./src
RUN ./gradlew bootJar --no-daemon

# Extract layers for Docker caching
RUN java -Djarmode=tools -jar build/libs/*.jar extract --launcher --layers --destination extracted

FROM bellsoft/liberica-openjre-alpine:25
WORKDIR /app

# Copy extracted layers
COPY --from=builder /build/extracted/dependencies/ ./
COPY --from=builder /build/extracted/spring-boot-loader/ ./
COPY --from=builder /build/extracted/snapshot-dependencies/ ./
COPY --from=builder /build/extracted/application/ ./

EXPOSE 8080
USER nobody
ENTRYPOINT ["java", "org.springframework.boot.loader.launch.JarLauncher"]
