# java-callflow-01

Test project for java callflow analysis

## Development

### Requirements

* Java 17
* Apache Maven 3.9+

### Build

```bash
mvn clean package

# or to build the distribution package
mvn clean package -Pdist
```

### Run

```bash
unzip -d target/dist target/jenkins-examples-callflow-*-dist.zip
java -jar target/dist/jenkins-examples-callflow-*/lib/jenkins-examples-callflow-1-SNAPSHOT.jar
```
