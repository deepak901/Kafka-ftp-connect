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


{
  "name": "spooldir-transform-metadata-connector",
  "config": {
    "connector.class": "com.github.jcustenborder.kafka.connect.spooldir.SpoolDirJsonSourceConnector",
    "tasks.max": "1",
    "input.path": "/data/json-files",
    "input.file.pattern": ".*\\.json",

    // Send transformed data to this topic
    "topic": "transformed-data-topic",

    // Data transformations
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
   
    "transforms": "MaskField,InsertFileMetadata,RouteToMetadataTopic",
   
    // Example: Mask or remove specific fields from the data (transforming it)
    "transforms.MaskField.type": "org.apache.kafka.connect.transforms.MaskField$Value",
    "transforms.MaskField.fields": "sensitiveField1,sensitiveField2",

    // Extract file metadata
    "transforms.InsertFileMetadata.type": "com.github.jcustenborder.kafka.connect.spooldir.SpoolDirSourceConnector$Value",
    "transforms.InsertFileMetadata.file.name": "fileName",
    "transforms.InsertFileMetadata.file.absolute.path": "filePath",
    "transforms.InsertFileMetadata.file.modified.time": "lastModified",
   
    // Route the metadata to another topic
    "transforms.RouteToMetadataTopic.type": "org.apache.kafka.connect.transforms.RegexRouter",
    "transforms.RouteToMetadataTopic.regex": ".*",
    "transforms.RouteToMetadataTopic.replacement": "metadata-topic"
  }
}
