FROM eclipse-temurin:21-jdk AS build
WORKDIR /app
COPY . .
RUN chmod +x mvnw && ./mvnw clean package -DskipTests

FROM eclipse-temurin:21-jre
RUN apt-get update \
 && apt-get install -y --no-install-recommends ffmpeg curl ca-certificates git gnupg \
 && curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
 && apt-get install -y --no-install-recommends nodejs \
 && rm -rf /var/lib/apt/lists/* \
 && curl -fL https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp_linux -o /usr/local/bin/yt-dlp \
 && chmod a+rx /usr/local/bin/yt-dlp \
 && git clone --depth 1 --single-branch --branch 2.0.0 https://github.com/Brainicism/bgutil-ytdlp-pot-provider.git /opt/bgutil \
 && cd /opt/bgutil/server && npm ci && npx tsc \
 && mkdir -p /etc/yt-dlp/plugins \
 && curl -fL https://github.com/Brainicism/bgutil-ytdlp-pot-provider/releases/download/2.0.0/bgutil-ytdlp-pot-provider.zip -o /etc/yt-dlp/plugins/bgutil-ytdlp-pot-provider.zip \
 && echo "--extractor-args youtubepot-bgutilscript:server_home=/opt/bgutil/server" > /etc/yt-dlp.conf
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
CMD ["sh", "-c", "cp /etc/secrets/cookies.txt /tmp/cookies.txt 2>/dev/null; yt-dlp -U; (sleep 90; yt-dlp -v --skip-download --cookies /tmp/cookies.txt https://youtu.be/TS8wb9IMHA4 2>&1 | grep -iE 'po token|bgutil|error|sign in' | head -n 25) & java -Xmx256m -jar app.jar --server.port=${PORT:-8080}"]
