FROM ubuntu:18.04

COPY . .

RUN apt-get update && apt-get install -y maven

CMD ["mvn", "package"]
