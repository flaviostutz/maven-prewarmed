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

# Libraries

Some libs warmedup includes:

```
		<kafka.version>1.1.0</kafka.version>
		<javaxwsrs.version>2.0.1</javaxwsrs.version>
		<prometheus.version>0.3.0</prometheus.version>
		<log4j.version>2.10.0</log4j.version>
		<jersey.version>2.25</jersey.version>
		<jetty.version>9.2.24.v20180105</jetty.version>
		<jackson.version>2.9.4</jackson.version>
```
