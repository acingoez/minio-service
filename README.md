# MinIO Service

MinIO Object Storage Service für Dokumenten-Management.

## Features

- **Object Storage**: S3-kompatible API
- **Web Console**: MinIO Management Interface
- **Docker Integration**: Läuft im pg-database-network
- **Persistent Storage**: Daten bleiben erhalten

## Konfiguration

### Ports
- **API Port**: 9000 (extern und intern)
- **Console Port**: 9001 (extern und intern)

### Zugangsdaten
- **Username**: minioadmin
- **Password**: minioadmin123

### Service-Name
- **Container**: minio-service
- **DNS**: minio-service (im pg-database-network)

## Installation

```bash
# MinIO Service starten
docker-compose up -d

# Status prüfen
docker-compose ps

# Logs anzeigen
docker-compose logs -f minio
```

## Verwendung

### Web Console
- **URL**: http://localhost:9001
- **Login**: minioadmin / minioadmin123

### API Zugriff
- **Endpoint**: http://localhost:9000
- **S3-kompatibel**: Alle S3-Clients funktionieren

### Service-Integration
```javascript
// Von anderen Services im pg-database-network
const minioEndpoint = 'http://minio-service:9000';
const accessKey = 'minioadmin';
const secretKey = 'minioadmin123';
```

## Bucket-Erstellung

Nach dem Start müssen Buckets erstellt werden:

1. **Web Console öffnen**: http://localhost:9001
2. **Login**: minioadmin / minioadmin123
3. **Bucket erstellen**: z.B. "documents"

## Integration mit Document-Service

Der Document-Service kann MinIO wie folgt verwenden:

```javascript
// MinIO Client Konfiguration
const Minio = require('minio');

const minioClient = new Minio.Client({
  endPoint: 'minio-service',
  port: 9000,
  useSSL: false,
  accessKey: 'minioadmin',
  secretKey: 'minioadmin123'
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
# Andere Services auf Port 9000/9001 stoppen
lsof -i :9000
lsof -i :9001
```

### Daten-Persistierung
```bash
# Volume-Status prüfen
docker volume ls | grep minio

# Daten sichern
docker run --rm -v minio_data:/data -v $(pwd):/backup alpine tar czf /backup/minio-backup.tar.gz -C /data .
```
