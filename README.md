# Kafka-ftp-connect


Invoke-RestMethod -Uri "http://localhost:8083/connectors" -Method Post -Headers @{"Content-Type" = "application/json"} -Body '{
  "name": "spooldir-json-connector",
  "config": {
    "connector.class": "com.github.jcustenborder.kafka.connect.spooldir.SpoolDirJsonSourceConnector",
    "tasks.max": "1",
    "topic": "files",
    "input.path": "/data/unprocessed",
    "input.file.pattern": ".*\\.json",
    "error.path": "/data/error",
    "finished.path": "/data/processed",
    "halt.on.error": "false",
    "schema.generation.enabled": "true",
    "schema.generation.key.fields": "id",
    "schema.generation.key.name": "com.example.Key",
    "schema.generation.value.name": "com.example.Value",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",
    "poll.interval.ms": "2000"
  }
}'
