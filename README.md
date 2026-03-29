# log-file-generation

## filebeat.yml

  filebeat.inputs:
  
    - type: filestream
      id: payment-service-logs
      enabled: true
      paths:
        - D:/logs/car-service/*.log
      fields:
        service: car-service
        environment: production
      fields_under_root: true
  
  output.kafka:
  
    hosts: ["172.16.30.10:9092"]
    topic: "app-logs"
    codec.json:
      pretty: false
