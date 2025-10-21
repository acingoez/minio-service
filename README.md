# MinIO Service

MinIO Object Storage Service für Dokumenten-Management.

## Features

- **Object Storage**: S3-kompatible API
- **Web Console**: MinIO Management Interface
- **Docker Integration**: Läuft im pg-database-network
- **Persistent Storage**: Daten bleiben erhalten

## Konfiguration

### Ports
- **API Port**: ${MINIO_PORT} (Standard: 40002, extern und intern)

### Zugangsdaten
- **Username**: ${MINIO_ROOT_USER} (Standard: minioadmin)
- **Password**: ${MINIO_ROOT_PASSWORD} (Standard: minioadmin123)

### Service-Name
- **Container**: minio-service
- **DNS**: minio-service (im pg-database-network)

## Installation

```bash
# Environment-Datei erstellen
cp env.example .env

# MinIO Service starten
docker-compose up -d

# Status prüfen
docker-compose ps

# Logs anzeigen
docker-compose logs -f minio
```

## Verwendung

### API Zugriff
- **Endpoint**: http://localhost:${MINIO_PORT}
- **S3-kompatibel**: Alle S3-Clients funktionieren

### Service-Integration
```javascript
// Von anderen Services im pg-database-network
const minioEndpoint = `http://minio-service:${process.env.MINIO_PORT || '40002'}`;
const accessKey = process.env.MINIO_ROOT_USER || 'minioadmin';
const secretKey = process.env.MINIO_ROOT_PASSWORD || 'minioadmin123';
```

## Bucket-Erstellung

Nach dem Start müssen Buckets erstellt werden:

1. **API verwenden**: MinIO API auf Port ${MINIO_PORT}
2. **Bucket erstellen**: z.B. "documents" über API oder S3-Client

## Integration mit Document-Service

Der Document-Service kann MinIO wie folgt verwenden:

```javascript
// MinIO Client Konfiguration
const Minio = require('minio');

const minioClient = new Minio.Client({
  endPoint: 'minio-service',
  port: parseInt(process.env.MINIO_PORT || '40002'),
  useSSL: false,
  accessKey: process.env.MINIO_ROOT_USER || 'minioadmin',
  secretKey: process.env.MINIO_ROOT_PASSWORD || 'minioadmin123'
});
```

## Troubleshooting

### Service nicht erreichbar
```bash
# Container-Status prüfen
docker-compose ps

# Logs anzeigen
docker-compose logs minio

# Netzwerk prüfen
docker network inspect pg-database-network
```

### Port-Konflikte
```bash
# Andere Services auf Port ${MINIO_PORT} stoppen
lsof -i :${MINIO_PORT:-40002}
```

### Daten-Persistierung
```bash
# Volume-Status prüfen
docker volume ls | grep minio

# Daten sichern
docker run --rm -v minio_data:/data -v $(pwd):/backup alpine tar czf /backup/minio-backup.tar.gz -C /data .
```
