FROM ubuntu

# WORKDIR instruction
WORKDIR /lab4

# RUN instruction
RUN apt-get update && apt-get install -y
# Create a redis user and use gosu to change another user to execute the command
RUN groupadd -r redis && useradd -r -g redis redis

# USER instruction
USER redis

# ENTRYPOINT, RUN instruction
ENTRYPOINT ["/bin/echo", "Hello World!"]
CMD ["DOCKER"]
