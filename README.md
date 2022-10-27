# Motivation
This project is a reproducer to demonstrate that a Quarkus app doesn't restart
on an OutOfMemoryError (OOM).

It is based on the code-with-quarkus template codebase.

## Building
```bash
mvn clean install -DskipTests=true
```

## Running the server
```bash
java -Xmx256M -jar target/code-with-quarkus-1.0.0-SNAPSHOT-runner.jar
```

## Reproducer
In another terminal, run:
```bash
curl http://localhost:8080/hello
curl http://localhost:8080/hello
curl http://localhost:8080/hello
```

### Expected behaviour
The app server terminates after the OOM error with the first `curl` command.

### Current behaviour
The app server continues running.

However, using the JVM flag
`-XX:+ExitOnOutOfMemoryError` will terminate the app server on an
OutOfMemoryError.

## Discussion
I am wondering if for Quarkus, it is the expected behaviour to not terminate on
OOM to allow the app to recover.

This is however, counter-intuitive when using Quarkus in a container in
Kubernetes, since we want the app to terminate so that a new pod is started
with fresh state.
