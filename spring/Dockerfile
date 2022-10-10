### build stage ###
FROM adoptopenjdk/openjdk11 AS builder

# set arg
ARG WORKSPACE=/home/spring-docker
ARG BUILD_TARGET=${WORKSPACE}/build/libs
WORKDIR ${WORKSPACE}

# copy code & build
COPY gradlew .
COPY gradle gradle
COPY build.gradle .
COPY settings.gradle .
COPY src src

RUN chmod +x ./gradlew
RUN ./gradlew clean bootJar

# unpack jar
WORKDIR ${BUILD_TARGET}
RUN jar -xf *.jar


### create image stage ###
FROM openjdk:11

ARG WORKSPACE=/home/spring-docker
ARG BUILD_TARGET=${WORKSPACE}/build/libs
ARG DEPLOY_PATH=${WORKSPACE}/deploy

# copy from build stage
COPY --from=builder ${BUILD_TARGET}/org ${DEPLOY_PATH}/org
COPY --from=builder ${BUILD_TARGET}/BOOT-INF/lib ${DEPLOY_PATH}/BOOT-INF/lib
COPY --from=builder ${BUILD_TARGET}/META-INF ${DEPLOY_PATH}/META-INF
COPY --from=builder ${BUILD_TARGET}/BOOT-INF/classes ${DEPLOY_PATH}/BOOT-INF/classes

WORKDIR ${DEPLOY_PATH}

EXPOSE 8080/tcp
#ENTRYPOINT ["java", "-Dspring.profiles.active=${PROFILE}", "-jar", "/app.jar"]
ENTRYPOINT ["java", "-Dspring.profiles.active=${PROFILE}", "org.springframework.boot.loader.JarLauncher"]


#FROM adoptopenjdk/openjdk11 AS builder
#COPY gradlew .
#COPY gradle gradle
#COPY build.gradle .
#COPY settings.gradle .
#COPY src src
#RUN chmod +x ./gradlew
#RUN ./gradlew bootJar
#
#FROM adoptopenjdk/openjdk11
#COPY --from=builder build/libs/*.jar app.jar
#
#EXPOSE 8080
#ENTRYPOINT ["java", "-Dspring.profiles.active=${PROFILE}", "-jar", "/app.jar"]