---
title: quarkus with gradle
what: rest client
date: nov 2019
---

Quarkus Rest Client 
===================

Get information about countries via a RESTful API

## Getting Started

This example uses Gradle and [Quarkus][quark] to build a REST client
that queries the [restcountries.eu][restc] API to find country information.

The example was built and tested using: 

- openjdk version "1.8.0_202" (Azul Zulu 8.0.202-zulu)
- openjdk version "1.8.0_232" (GraalVM 19.3.0.r8-grl)

### Prerequisites

- Java 8
- Gradle 

### Getting Started

#### Installing: 

```bash
$ git clone https://github.com/petertorres/rest-client.git
$ cd rest-client
```

#### Running the example:

```bash
$ ./gradlew quarkusDev
#Endpoint:
$ curl http://localhost:8080/country/name/france
#Async endpoint:
$ curl http://localhost:8080/country/name-async/france
``` 

#### Running tests:

```bash 
$ ./gradlew cleanTest test                                                           
```

#### Building the example:

```bash
$ ./gradlew quarkusBuild
```

#### Building an uber(fat) jar:
```bash
$ ./gradlew quarkusBuild --uber-jar
```

#### Building as a native binary:

>Note that the [GraalVM][graal] JDK is required for building native binaries.

>Using [sdkman](https://sdkman.io):

>```bash
 $ sdk install java 19.3.0.r8-grl
 $ gu install native-image
 $ export GRAALVM_HOME=~/.sdkman/candidates/java/19.3.0.r8-grl
>``` 

```bash
$ ./gradlew build
#Or
$ ./gradlew buildNative
```

By default, `./gradlew build` will build a native image. To skip, use:

```bash
$ ./gradlew build -x buildNative -x testNative
```

#### Building as a container:

>Note that a pair of Dockerfiles for native and jvm mode are also generated 
in `src/main/docker`. Instructions to build the image and run the container 
are written in those Dockerfiles.

```bash
$ ./gradlew buildNative --docker-build=true
```

## Doing More Stuff

Quarkus provides an online configuration [tool][codeq] for creating new 
Maven or Gradle projects.  

The Quarkus Gradle [tooling][gradl] allows for additional 
tasks, such as connecting a debugger at runtime, adding extensions and 
building containerized applications with Docker.  Reference the Quarkus 
Gradle [page][gradl] for details or run `./gradlew tasks` for a list of 
Quarkus tasks.  

### Using Configuration Properties

Quarkus relies on Eclipse [MicroProfile][micro] to inject configuration 
values into an application. Use the `@ConfigProperty` annotation to inject 
a configuration value from `src/main/resources/application.properties`.  
Or use [`ConfigProperties`][cfprs] to group multiple properties together.  

Quarkus also provides a list of framework specific [properties][cfall] that 
can be configured.  See the [configuration][confi] page for details.  

#### Injecting values from property file with `@ConfigProperty`:

```java
//application.properties
greeting.message = hello
greeting.name = quarkus
```

```java
@ConfigProperty(name = "greeting.message") 
String message;

//With default value
@ConfigProperty(name = "greeting.suffix", defaultValue="!") 
String suffix;

//With optional value
@ConfigProperty(name = "greeting.name")
Optional<String> name;

//Use the injected values
@GET
@Produces(MediaType.TEXT_PLAIN)
public String hello() {
    return message + " " + name.orElse("world") + suffix;
}
```

### Extentions

#### Adding Extensions:

```bash
$ ./gradlew listExtensions
$ ./gradlew addExtension --extensions="hibernate-validator"
#Add multiple extensions at once:
$ ./gradlew addExtension --extensions="jdbc,agroal,non-exist-ent"
#Or all extensions that match a pattern:
$ ./gradlew addExtension --extensions="hibernate*"
```

#### Building a thin jar

### Gradle 

#### `build.gradle` addition to show details when running tests

```groovy
test {
    testLogging {
        //available events: "passed", "skipped", 
        // "failed", "standardOut", "standardError"
        events "passed", "skipped", "failed"
    }
}
```


## License

This project is not licensed.

[quark]:https://quarkus.io/
[restc]:https://restcountries.eu/
[gradl]:https://quarkus.io/guides/gradle-tooling
[graal]:https://www.graalvm.org/
[codeq]:https://code.quarkus.io/
[sdkmi]:https://sdkman.io/
[confi]:https://quarkus.io/guides/config
[cfall]:https://quarkus.io/guides/all-config
[cfprp]:https://quarkus.io/guides/config#configuration-profiles
[cfprs]:https://quarkus.io/guides/config#using-configproperties
[micro]:https://github.com/eclipse/microprofile-config/blob/master/spec/src/main/asciidoc/converters.asciidoc
