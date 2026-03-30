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
