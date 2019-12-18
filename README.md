
Quarkus Rest Client 
===================

Get information about countries via a RESTful API

## Getting Started

This example uses Gradle and [Quarkus][quark] to build a REST client
that queries the [restcountries.eu][restc] API to find country information.

The base example was created using the Quarkus online configuration [tool][codeq] 
for creating new Maven or Gradle projects.  The rest client was created from 
the Quarkus [rest client][quarg] guide.  

### Prerequisites

- JDK 8 

The example was built and tested using: 

OpenJDK   | Version    | sdkman release
----------|------------|---------------
Azul Zulu | 1.8.0_202 | 8.0.202-zulu
GraalVM   | 1.8.0_232 | 19.2.1-grl

Tool    | Version 
--------|----------
Quarkus | 1.0.0.CR2
Gradle  | 5.6.2 
Sdkman  | 5.7.4+362


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

#### Running the (thin) jar:

```bash
$ java -jar build/rest-client-1.0.0-SNAPSHOT-runner.jar
```

#### Building and run an uber(fat) jar:

```bash
$ ./gradlew quarkusBuild --uber-jar
#The thin jar is overwritten
$ java -jar build/rest-client-1.0.0-SNAPSHOT-runner.jar
```

#### Building as a native binary*:

>Note that the [GraalVM][graal] JDK is required for building native binaries.

>Using [sdkman](https://sdkman.io):

> `$ sdk install java 19.2.1-grl`  
> `$ gu install native-image`   
> `$ export GRAALVM_HOME=~/.sdkman/candidates/java/19.2.1-grl` 

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

### Adding Extensions

```bash
$ ./gradlew listExtensions
$ ./gradlew addExtension --extensions="hibernate-validator"
#Add multiple extensions at once:
$ ./gradlew addExtension --extensions="jdbc,agroal,non-exist-ent"
#Or all extensions that match a pattern:
$ ./gradlew addExtension --extensions="hibernate*"
```

## *Known Issues

Current issues encountered when building native binaries from the example.  
Review the Quarkus [tips][qtips] for writing native applications.

### Quarkus

- "These dependencies are not recommended" - Open (quarkus) [issue][issuq]  

### GraalVM

- "No instances of java.{SOMETHING} are allowed..." - Open (native-image) [issue][issun], [issue][issuo]  
**SOLUTION:** Use GraalVM version 19.2.1-grl

### MacOS

You may encounter an error when building native applications on MacOS about 
a missing `xcrun` path even though the binary may be found at `/usr/bin/xcrun`.

To resolve the issue, install the xcode CLI tools.

```bash
$ xcode-select --install
```

## Useful Output when Testing with Gradle 

`build.gradle` includes a logging configuration change to the test task that 
allows for rspec-style output.  

```groovy
//build.gradle
test {
    testLogging {
        //available events: 
        //"passed", "skipped", "failed", "standardOut", "standardError"
        events "passed", "skipped", "failed"
    }
}
```

```bash
$ ./gradlew cleanTest test           
> Task :test

org.peteness.ExampleResourceTest > testHelloEndpoint() PASSED

org.peteness.restclient.CountriesResourceTest > testCountryNameEndpoint() PASSED

org.peteness.restclient.CountriesResourceTest > testCountryNameAsyncEndpoint() PASSED
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
[qtips]:https://quarkus.io/guides/writing-native-applications-tips
[issuq]:https://github.com/quarkusio/quarkus/issues/4960
[issun]:https://github.com/oracle/graal/issues/1074
[issuo]:https://stackoverflow.com/questions/59011565/no-instances-of-are-allowed-in-the-image-heap-as-this-class-should-be-initia
[quarg]:https://quarkus.io/guides/rest-client
