# Processing Messages With Spring Cloud Data Flow

Following the instructions to get set up on PWS with Spring Cloud Data Flow (SCDF), https://github.com/lshannon/spring-cloud-data-flow-setup, let's explore a more detailed example.

## Basic Stream

We can create a simple stream.

```shell

stream create --name m --definition "http | log"

```

We can test the stream.

```shell

http post --target https://clouqa-dataflow-server-ok8lm7b-words-http.cfapps.io/ --data "Hello World"

```

A Spring Boot component can be created to produce messages.

```shell

@Bean
	public CommandLineRunner init(RestTemplate template) {
		return (args) -> {
		
			
			for (int i = 0; i < 10000; i++) {
				HttpEntity<String> payload = new HttpEntity<String>("A letter for you sir! : " + i);
				template.postForLocation("https://clouqa-dataflow-server-ok8lm7b-m2-http.cfapps.io/", payload);				
			}
		};
	}

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

stream create --name d --definition ":db > jdbc --driver-class-name=org.postgresql.Driver --username=xxxxxx --password=xxxxxxx --url=jdbc:postgresql://stampy.db.elephantsql.com:5432/xxxxxx --jdbc.initialize=true --spring.datasource.maxActive=2 --spring.datasource.tomcat.max-active=2" --deploy

```

## Update the Stream to Use Routes

Now let's send the data to the appropriate stream using the routing key the processor added.

```shell

stream create m2 --definition "http | simple-message-processor | rabbit --routing-key-expression=#jsonPath(payload,'$.routingKey') --exchange-expression=#jsonPath(payload,'$.routingKey')" --deploy

```










