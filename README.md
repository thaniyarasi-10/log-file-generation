# log-file-generation

## Log format

  {
    "timestamp": "2026-03-28T10:00:00.000Z",
    "level": "ERROR",
    "service": "payment-service",
    "instance": "pod-xyz-123",
    "environment": "production",
    "message": "Payment failed for user",
    "traceId": "abc123def456",
    "spanId": "span789",
    "userId": "u789",
    "endpoint": "/api/pay",
    "method": "POST",
    "statusCode": 500,
    "responseTime": 1200,
    "errorCode": "PAYMENT_GATEWAY_TIMEOUT",
    "errorDetails": "Connection timed out after 1200ms",
    "tags": {
      "team": "payments",
      "version": "2.1.0"
    }
  }

Download filebeat and setup filebeat.yml

filebeat.yml:

  filebeat.inputs:
  
    - type: filestream
      id: payment-service-logs (your id name)
      enabled: true
      paths:
        - D:/logs/car-service/*.log (your directory)
      fields:
        service: car-service (your service name)
        environment: production
      fields_under_root: true
      prospector.scanner.fingerprint.length: 64
      prospector.scanner.fingerprint.offset: 0

  processors:
        - decode_json_fields:
            fields: ["message"]
            target: ""           
            overwrite_keys: true 
      

  // do not change output
  output.kafka:
  
    hosts: ["172.16.30.10:9092"]
    topic: "app-logs"
    codec.json:
      pretty: false


## Java
 add logback.xml in resource folder

<configuration>

    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>D:/logs/car-catalog(your folder name)/app.log </file>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>D:/logs/car-catalog(your file name)/app-%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>7</maxHistory>
        </rollingPolicy>

        <encoder class="net.logstash.logback.encoder.LogstashEncoder">
            <fieldNames>
                <timestamp>timestamp</timestamp>
            </fieldNames>
            <customFields>{"service":"car-catalog","environment":"production"}</customFields>
        </encoder>
    </appender>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss} [%level] %logger - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="FILE"/>
    </root>

</configuration>

Add dependencies:
       

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jul-to-slf4j</artifactId>
            <version>2.0.17</version>
        </dependency>

        <dependency>
            <groupId>net.logstash.logback</groupId>
            <artifactId>logstash-logback-encoder</artifactId>
            <version>7.4</version>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.kafka</groupId>
            <artifactId>spring-kafka</artifactId>
            <version>3.1.2</version>
            <scope>compile</scope>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>2.0.13</version>
        </dependency>


