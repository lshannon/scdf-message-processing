# Processing Messages With Spring Cloud Data Flow

Following the instructions to get set up on PWS with Spring Cloud Data Flow (SCDF), https://github.com/lshannon/spring-cloud-data-flow-setup, let's explore a more detailed example.

## Basic Stream

We can create a simple stream.

```shell

stream create --name m --definition "http | log"

```

We can test the stream.

```shell

http post --target https://clouqa-dataflow-server-ok8lm7b-words-http.cfapps.io/ --date "Hello World"

```

## Adding Custom Transformation

Deploy the repository application with the transformer package. Then register the service.

```shell

app register --name simple-message-processor --type processor --uri https://processor-repository-roseolar-pauperage.cfapps.io/simple-mesage-processor-0.0.1-SNAPSHOT.jar

```

Create a new stream that uses the custom component.

```shell

stream create m2 --definition "http | simple-message-processor | log" --deploy

```

## Creating Routes

Lets create the streams for routing.

```shell

stream create --name l --definition ":log > log" --deploy

stream create --name d --definition ":db > jdbc --driver-class-name=org.postgresql.Driver --username=zggecdgv --password=WjyVASG9nF2RPNnAqpwqgxNbarCipyJA --url=jdbc:postgresql://stampy.db.elephantsql.com:5432/zggecdgv --jdbc.initialize=true" --deploy

```










