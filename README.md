# KUT-MSA-Design-Pattern-6

http://naver.me/5IT0vYh8

### VirtualBox 

          VBoxManage natnetwork add --netname NatNetwork --network "192.168.15.0/24" --enable --dhcp off --port-forward-4 "ssh:tcp:[]:22:[192.168.15.101]:22"
          VBoxManage natnetwork --network NatNetwork --port-forward-4 "ssh:tcp:[]:23:[192.168.15.102]:22"
          
          VBoxManage setextradata global GUI/Input/HostKeyCombination 162,164

### Visual Studio Code

            code --install-extension MS-CEINTL.vscode-language-pack-ko
            code --install-extension ms-vscode-remote.remote-ssh
            code --install-extension ms-azuretools.vscode-docker
            code --install-extension vscjava.vscode-java-pack
            code --install-extension vscjava.vscode-gradle
            code --install-extension vmware.vscode-boot-dev-pack
          
            copy id_rsa on Host Windows(C:\Users\사용자\.ssh)
          
            -- config --------------------------------------
            Host labserver
            HostName localhost
            Port 22101
            User user1
            IdentityFile C:\Users\사용자\.ssh\id_rsa
            ------------------------------------------------


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



          services:
            zipkin:
              image: ghcr.io/openzipkin/zipkin-slim:${TAG:-latest}
              container_name: zipkin
              environment:
                - STORAGE_TYPE=mem
                - MYSQL_HOST=mysql
                # - SELF_TRACING_ENABLED=true
                # - JAVA_OPTS=-Xms128m -Xmx128m -XX:+ExitOnOutOfMemoryError
              ports:
                - 9411:9411
              command: --logging.level.zipkin2=DEBUG
          
            rabbitmq:
              image: rabbitmq:latest
              container_name: rabbitmq
              ports:
                - 5672:5672
          
            mysql:
              image: mysql:latest
              container_name: mysql
              restart: always
              environment:
                MYSQL_ROOT_PASSWORD: 1234
                TZ: Asia/Seoul
              volumes:
                - ./database:/docker-entrypoint-initdb.d
              ports:
                - 3306:3306

### ELK
            elasticsearch:
              image: docker.elastic.co/elasticsearch/elasticsearch:7.7.0
              container_name: elasticsearch
              environment:
                - node.name=elasticsearch
                - discovery.type=single-node
                - cluster.name=docker-cluster
                - bootstrap.memory_lock=true
                - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
              ulimits:
                memlock:
                  soft: -1
                  hard: -1
              volumes:
                - esdata1:/usr/share/elasticsearch/data
              ports:
                - 9300:9300
                - 9200:9200
          
            kibana:
              image: docker.elastic.co/kibana/kibana:7.7.0
              container_name: kibana
              environment:
                ELASTICSEARCH_URL: "http://elasticsearch:9300"
              ports:
                - 5601:5601
          
            logstash:
              image: docker.elastic.co/logstash/logstash:7.7.0
              container_name: logstash
              command: logstash -f /etc/logstash/conf.d/logstash.conf
              volumes:
                - ./config:/etc/logstash/conf.d
              ports:
                - "5000:5000"
          
            zipkin: 
              image: openzipkin/zipkin 
              container_name: zipkin
              depends_on: 
                - elasticsearch
              environment: 
                - STORAGE_TYPE=elasticsearch
                - "ES_HOSTS=elasticsearch:9300"
              ports:
                - "9411:9411"

### logstash.config

                    input {
                      tcp {
                        port => 5000
                        codec => json_lines
                      }
                    }
                    
                    filter {
                      mutate {
                        add_tag => [ "manningPublications" ]
                      }
                    }
                    
                    output {
                      elasticsearch {
                        hosts => "elasticsearch:9200"
                      }
                    }

                    <dependency>
                    <groupId>net.logstash.logback</groupId>
                    <artifactId>logstash-logback-encoder</artifactId>
                    <version>6.3</version>
                    </dependency>
