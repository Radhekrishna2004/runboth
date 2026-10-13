 FROM eclipse-temurin:21-jdk AS build
WORKDIR /app
COPY . .
RUN chmod +x mvnw && ./mvnw clean package -DskipTests

FROM eclipse-temurin:21-jre
RUN apt-get update \
 && apt-get install -y --no-install-recommends ffmpeg curl ca-certificates \
 && rm -rf /var/lib/apt/lists/* \
 && curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp_linux -o /usr/local/bin/yt-dlp \
 && chmod a+rx /usr/local/bin/yt-dlp
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
CMD ["sh", "-c", "cp /etc/secrets/cookies.txt /tmp/cookies.txt; ls -l /etc/secrets /tmp/cookies.txt; (yt-dlp --cookies /tmp/cookies.txt --skip-download -v https://youtu.be/TS8wb9IMHA4 2>&1 | tail -n 25 &); java -Xmx400m -jar app.jar --server.port=${PORT:-8080}"]
