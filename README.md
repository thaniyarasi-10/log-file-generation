# log-file-generation

## filebeat.yml

filebeat.inputs:
  - type: filestream
    id: all-app-logs
    enabled: true
    paths:
      - D:/logs/*.log

output.kafka:
  hosts: ["172.16.30.10:9092"]
  topic: "app-logs"
