FROM alpine:3.9.3

#Setting a default value to Argument WELCOME_USER
ARG WELCOME_USER="Quan Blue"
ENV WELCOME_MESSAGE="[ENV] Welcome to Docker World"

RUN echo "[ARG] Welcome ${WELCOME_USER}, to Docker World!" > arg_message.txt


CMD cat arg_message.txt && echo "${WELCOME_MESSAGE}"
