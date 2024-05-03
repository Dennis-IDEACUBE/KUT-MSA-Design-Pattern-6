# KUT-MSA-Design-Pattern-6

### ZipKin

        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-prometheus</artifactId>
        </dependency>
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-tracing-bridge-brave</artifactId>
            <version>1.1.4</version>
        </dependency>
        <dependency>
            <groupId>io.zipkin.reporter2</groupId>
            <artifactId>zipkin-reporter-brave</artifactId>
        </dependency>

        logging:
          pattern:
            level: "%5p [%X{traceId},%X{spanId}]"
        management:
          tracing:
            sampling:
              probability: 1.0
            propagation:
              consume: b3
              produce: b3
            enabled: true
          zipkin:
            tracing:
              endpoint: http://localhost:9411/api/v2/spans
          endpoints:
            web:
              exposure:
                include: "prometheus"      
