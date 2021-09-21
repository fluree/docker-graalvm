# graalvm Docker image

This image contains the GraalVM JDK with the native-image tool pre-installed.

## Usage

This image is intended to be built on top of in your own Dockerfiles. That is, it should appear in the `FROM` line of your own Dockerfiles for building native executables from JVM-based languages.

Normally at runtime you would then use a much smaller image that only has the necessary runtime shared libraries you need.

## Example

```Dockerfile
FROM graalvm AS builder

COPY my-app.jar .

RUN native-image -jar my-app.jar

FROM debian:bullseye-slim AS runner

COPY --from=builder ./my-app

CMD ["./my-app"]
```
