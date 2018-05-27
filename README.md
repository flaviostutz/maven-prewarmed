# maven-prewarmed
Maven docker image prewarmed with common Java build plugins so that it won't download them everytime

# Usage

In your Dockerfile, do something like:

```
FROM flaviostutz/maven-prewarmed:3.5-jdk-8 as BUILD
WORKDIR /build

#optimize for reusing local cache during builds
ADD ./pom.xml /build/pom.xml
RUN mvn package

ADD ./ /build/
RUN mvn package


FROM openjdk:8u162-jre-stretch

WORKDIR /app
COPY --from=BUILD /build/target/*-with-dependencies.jar /app/
RUN cd /app && \
    mv $(ls *-with-dependencies.jar | head -1) /app/app.jar

EXPOSE 3000

CMD ["sh","-c","java -jar /app/app.jar"]
```
