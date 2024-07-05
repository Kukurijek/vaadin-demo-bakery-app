Vaadin Demo Bakery App
=======================
![Master Branch](https://github.com/Kukurijek/vaadin-demo-bakery-app/actions/workflows/build.yml/badge.svg?branch=master)

# Running the Project in Production Mode with Docker 
```bash
 docker build --rm -t bakery-app .
 docker run --name bakery-app -d bakery-app
 docker logs -f bakery-app
```
The application is available at `http://container-ip:8080/`. To get the container IP address, execute the following command:
```console
 docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' bakery-app
```
Via the host machine on port 80:
```console
 docker run --name bakery-app -p 80:8080 -d bakery-app
```
The application is available at `http://localhost:80/` or `http://host-ip:80/`.

... or with an [image from Docker Hub](https://hub.docker.com/r/ibaiborodine/vaadin-bakery-app):
```console
 docker run --name bakery-app -p 80:8080 -d ibaiborodine/vaadin-bakery-app
```

# Running the Project in Development Mode

`mvn spring-boot:run`

Wait for the application to start

Open http://localhost:8080/ to view the application.

Default credentials are admin@vaadin.com/admin for admin access and
barista@vaadin.com/barista for normal user access.

Note that when running in development mode, the application will not work in IE11.

# Running Integration Tests and Linter

Integration tests are implemented using TestBench. The tests take tens of minutes to run and are therefore included in a separate profile. We recommend running tests with a production build to minimize the chance of development time toolchains affecting test stability. To run the tests, execute:

`mvn verify -Pit,production`

and make sure you have a valid TestBench license installed.

Profile `it` adds the following parameters to run integration tests:
```sh
-Dwebdriver.chrome.driver=path_to_driver
-Dcom.vaadin.testbench.Parameters.runLocally=chrome
```

if you would like to run a separate test make sure you have added these parameters to VM Options of JUnit run configuration

Run linter to check frontend code by adding `-DrunLint` to build/run command.

# Automatic Restart and Live Reload

To activate spring-boot-devtools is needed to:
1. Add spring-boot-devtools dependency
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
    <scope>runtime</scope>
</dependency>
```
2. Fork the process used to run the application by changing spring-boot-maven-plugin configuration
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>${spring-boot.version}</version>
    <configuration>
        <fork>true</fork>
    </configuration>
</plugin>
```
3. Optionally you might want to avoid the data generator to be run on each single reload, therefore, make H2 database store entities in file-system instead of in memory by adding the following lines to the `src/main/resources/application.properties`
```properties
spring.datasource.url=jdbc:h2:file:~/bakery-test-data
spring.jpa.hibernate.ddl-auto=update
```
To trigger the restart it is needed to update classpath.
In Eclipse it can be done automatically after save modified file.
In IntelliJ IDEA can be done manually `Build -> Build Project`

Live reload is supported and browser extensions can be found at http://livereload.com/extensions/.

# Running the Project in Production Mode

`mvn spring-boot:run -Pproduction`

The default mode when the application is built or started is 'development'. The 'production' mode is turned on by enabling the `production` profile when building or starting the app.

In the 'production' mode all frontend resources of the application are passed through the `polymer build` command, which minifies them and outputs two versions: for ES5- and ES6-supporting browsers. That adds extra time to the build process, but reduces the total download size for clients and allows running the app in browsers that do not support ES6 (e.g. in Internet Explorer 11).

Note that if you switch between running in production mode and development mode, you need to do
```sh
mvn clean
```
before running in the other mode.


# Running Scalability Tests

The Bakery App Starter includes scalability tests. Once you have deployed a production build of Bakery you can run them to check how the app behaves under load. The scalability tests can be run completely on your local machine, but you might as well want to run locally only the test agents while the Bakery app under test is deployed to an environment that is close to your production.

In order to run the scalability tests locally:

1. Make sure you are using Java 8 (Gatling Maven plugin does not yet work with Java 9+)

1. Build and start Bakery in the production mode (e.g. ```mvn clean spring-boot:run -DskipTests -Pproduction```)

1. Open terminal in the project root

1. Start a test from the command line:

    ```sh
    mvn -Pscalability gatling:test
    ```

1. Test results are stored into target folder (e.g. to ```target/gatling/BaristaFlow-1487784042461/index.html```)

1. By default, the scalability test starts 100 user sessions at a 100 ms interval for one repeat, all of which connect to a locally running Bakery app. These defaults can be overridden with the `gatling.sessionCount`, `gatling.sessionStartInterval` `gatling.sessionRepeats`, and `gatling.baseUrl` system properties. See an example execution for 300 users started within 50 s:

    ```sh
    mvn -Pscalability gatling:test -Dgatling.sessionCount=300 -Dgatling.sessionStartInterval=50
    ```

Note: If you run Bakery with an in-memory database (like H2, which is the default), it will logically use more memory than when using an external database (like PostgreSQL). It is recommend to run scalability tests for Bakery only after you have configured it to use an external database.

## Trivy
A comprehensive and versatile security scanner - Aqua Security

### Instalation
on macOS run
```sh
brew install trivy
```
to scan local filesystem run
```sh
trivy fs .
```
to explore more options run
```sh
trivy -h
```

## Local testing
run

```sh
docker-compose up --build
```