# Amoscloud AI - Deployment Configuration Guide

This document provides complete deployment instructions for Amoscloud AI across all platforms.

## Quick Start

### Local Development (Python)
```bash
# Clone repository
git clone https://github.com/gmmakulu-source/Amosclaud-.git
cd Amosclaud-

# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # macOS/Linux
# or
.venv\Scripts\Activate.ps1  # Windows

# Install dependencies
pip install --upgrade pip
pip install -r requirements.txt

# Run application
python -m amoscloud_ai.main

# Access application
# - Web: http://localhost:8000
# - API Docs: http://localhost:8000/docs
# - Health: http://localhost:8000/health
```

### Docker Deployment
```bash
# Build and run with Docker Compose
docker compose up --build

# Or run specific services
docker compose up -d api web redis postgres
docker compose logs -f api

# Stop services
docker compose down
```

## Production Deployment

### Railway (Recommended)

#### Prerequisites
- Railway account (railway.app)
- GitHub repository connected
- Persistent volume for `/data`

#### Environment Variables
```env
# Required
AUTH_DB_PATH=/data/auth.db
AUTH_COOKIE_SECURE=true
AUTH_SESSION_DAYS=7
AMOSCLAUD_MASTER_KEY=generate-with-$(python -c "import secrets; print(secrets.token_hex(32))")
REDIS_URL=redis://redis:6379/0

# Recommended
AMOS_MAIL_DOMAIN=yourdomain.com
PASSKEY_RP_ID=yourdomain.com
PASSKEY_ORIGIN=https://yourdomain.com
PASSKEY_RP_NAME=Amosclaud
PASSKEY_SETUP_MINUTES=10

# Optional - GitHub Integration
GITHUB_CLIENT_ID=your-client-id
GITHUB_CLIENT_SECRET=your-client-secret
GITHUB_CALLBACK_URL=https://yourdomain.com/api/v1/auth/github/callback

# Optional - Email Integration
MAIL_SMTP_HOST=your-smtp-host
MAIL_SMTP_PORT=587
MAIL_SMTP_USERNAME=your-username
MAIL_SMTP_PASSWORD=your-password
MAIL_SMTP_FROM=noreply@yourdomain.com
MAIL_SMTP_TLS=true

# Optional - Metrics
AMOSCLAUD_METRICS_TOKEN=your-metrics-token
```

#### Deploy to Railway
```bash
# Install Railway CLI
npm install -g @railway/cli

# Login to Railway
railway login

# Link project
railway link

# Deploy
railway up

# View logs
railway logs
```

#### Railway Volume Setup
1. Go to Railway project dashboard
2. Add volume: Mount at `/data`
3. Set size to at least 10GB
4. This volume persists authentication, mail, and sessions

#### Health Check
```bash
curl https://yourdomain.railway.app/health
# Expected response:
# {"status":"ok","timestamp":"2026-07-16T...","version":"1.0.1"}
```

### Kubernetes Deployment

#### Prerequisites
- kubectl configured
- Docker registry access
- Persistent volume provisioner

#### ConfigMap & Secrets
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: amoscloud
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: amoscloud-config
  namespace: amoscloud
data:
  AUTH_COOKIE_SECURE: "true"
  AUTH_SESSION_DAYS: "7"
  AMOS_MAIL_DOMAIN: "yourdomain.com"
  PASSKEY_RP_ID: "yourdomain.com"
  PASSKEY_ORIGIN: "https://yourdomain.com"
---
apiVersion: v1
kind: Secret
metadata:
  name: amoscloud-secrets
  namespace: amoscloud
type: Opaque
stringData:
  AMOSCLAUD_MASTER_KEY: "generate-secure-key"
  REDIS_URL: "redis://redis:6379/0"
```

#### StatefulSet Deployment
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: amoscloud-api
  namespace: amoscloud
spec:
  serviceName: amoscloud-api
  replicas: 2
  selector:
    matchLabels:
      app: amoscloud-api
  template:
    metadata:
      labels:
        app: amoscloud-api
    spec:
      containers:
      - name: api
        image: amoscloud/api:latest
        ports:
        - containerPort: 8000
        envFrom:
        - configMapRef:
            name: amoscloud-config
        - secretRef:
            name: amoscloud-secrets
        env:
        - name: AUTH_DB_PATH
          value: /data/auth.db
        volumeMounts:
        - name: data
          mountPath: /data
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 10
          periodSeconds: 5
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```

#### Service & Ingress
```yaml
apiVersion: v1
kind: Service
metadata:
  name: amoscloud-api
  namespace: amoscloud
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 8000
  selector:
    app: amoscloud-api
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: amoscloud-ingress
  namespace: amoscloud
spec:
  ingressClassName: nginx
  rules:
  - host: yourdomain.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: amoscloud-api
            port:
              number: 80
  tls:
  - hosts:
    - yourdomain.com
    secretName: amoscloud-tls
```

#### Deploy to Kubernetes
```bash
# Create namespace and deploy
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/secrets.yaml
kubectl apply -f k8s/statefulset.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml

# Check deployment
kubectl get pods -n amoscloud
kubectl logs -n amoscloud deployment/amoscloud-api
```

## Android App Deployment

### Building APK

#### Requirements
- Android Studio Hedgehog+
- JDK 17
- Android SDK 34
- Gradle 8.7

#### Debug Build
```bash
cd android
./gradlew assembleDebug

# Output: app/build/outputs/apk/debug/app-debug.apk
```

#### Release Build (Signed)
```bash
# Create keystore (one-time)
keytool -genkey -v -keystore amoscloud-release.jks \
  -keyalg RSA -keysize 2048 -validity 10000 \
  -alias amoscloud-release

# Build signed APK
./gradlew assembleRelease

# Output: app/build/outputs/apk/release/app-release.apk
```

#### Install on Device/Emulator
```bash
# Debug
./gradlew installDebug

# Release
adb install -r app/build/outputs/apk/release/app-release.apk
```

### Google Play Store Deployment

1. Create Google Play Developer account ($25 one-time fee)
2. Create App in Google Play Console
3. Prepare signed release APK
4. Upload APK to internal testing
5. Add app screenshots, description, privacy policy
6. Request review (3-7 days)
7. Launch to production

## Web App Deployment

### GitHub Pages (PWA)

```bash
# Build web app for production
npm run build  # in web/ directory

# Deploy to GitHub Pages
npm run deploy
```

### Custom Domain with HTTPS

```bash
# Update CNAME file
echo "yourdomain.com" > CNAME

# Configure GitHub Pages
# Settings > Pages > Custom domain: yourdomain.com
# Enable HTTPS enforcement
```

## Monitoring & Health Checks

### Health Endpoint
```bash
curl http://localhost:8000/health
```

Response:
```json
{
  "status": "ok",
  "timestamp": "2026-07-16T22:16:27.123456",
  "version": "1.0.1",
  "database": "connected",
  "redis": "connected",
  "uptime_seconds": 3600
}
```

### Metrics Collection (Prometheus)
```bash
# Metrics endpoint: http://localhost:9090/metrics
# Configure Prometheus to scrape:
# - job_name: 'amoscloud'
#   static_configs:
#   - targets: ['localhost:9090']
```

### Logging

#### Local Development
```bash
# Set log level
export LOG_LEVEL=DEBUG
python -m amoscloud_ai.main
```

#### Docker Logging
```bash
# View logs
docker compose logs -f api

# Follow specific service
docker compose logs -f postgres
```

#### Production Logging
- Logs written to: `/data/logs/amoscloud.log`
- Rotate logs daily
- Configure with `LOG_LEVEL` environment variable

## Database Migrations

### Automatic (Recommended)
Migrations run automatically on startup with checksum protection.

### Manual Migration
```bash
# Create migration
python -m alembic revision --autogenerate -m "description"

# Apply migrations
python -m alembic upgrade head

# Rollback
python -m alembic downgrade -1
```

## Backup & Recovery

### Database Backup
```bash
# PostgreSQL backup
pg_dump amoscloud_db > backup_$(date +%Y%m%d).sql

# Restore
psql amoscloud_db < backup_YYYYMMDD.sql
```

### SQLite Backup (Local)
```bash
# Copy database file
cp /data/auth.db /data/backups/auth_$(date +%Y%m%d).db
```

### Automated Backups
Configure in `docker-compose.yml`:
```yaml
backup:
  image: alpine
  volumes:
    - postgres_data:/var/lib/postgresql/data
    - ./backups:/backups
  command: |
    sh -c "
    while true; do
      pg_dump -h postgres -U postgres amoscloud_db | gzip > /backups/backup_$(date +%Y%m%d_%H%M%S).sql.gz
      find /backups -name 'backup_*.sql.gz' -mtime +7 -delete
      sleep 86400
    done
    "
```

## Security Hardening

### Environment Security
- ✅ Set `AUTH_COOKIE_SECURE=true` in production
- ✅ Use HTTPS only (redirect HTTP to HTTPS)
- ✅ Generate strong `AMOSCLAUD_MASTER_KEY`
- ✅ Rotate secrets regularly
- ✅ Use network policies to restrict access

### Network Security
```bash
# Firewall rules
sudo ufw allow 443  # HTTPS
sudo ufw allow 80   # HTTP (redirect only)
sudo ufw deny 8000  # Block internal port
```

### SSL/TLS Certificate
```bash
# Using Let's Encrypt with Certbot
sudo certbot certonly --standalone -d yourdomain.com
sudo certbot renew --dry-run
```

## Troubleshooting

### Application Won't Start
```bash
# Check environment variables
env | grep AMOSCLAUD

# Check logs
docker compose logs api

# Verify database connection
python -c "import os; print(os.getenv('REDIS_URL'))"
```

### High Memory Usage
```bash
# Check memory stats
docker stats amoscloud

# Limit container memory
docker run -m 2g amoscloud/api
```

### Database Connection Issues
```bash
# Test connection
psql -h localhost -U postgres -d amoscloud_db -c "SELECT 1"

# Check connection pool
SELECT count(*) FROM pg_stat_activity;
```

## Version Management

### Update Procedure
1. Pull latest code: `git pull origin main`
2. Review CHANGELOG.md
3. Run tests: `pytest tests/`
4. Back up database
5. Run migrations: `alembic upgrade head`
6. Restart application

---

**Last Updated:** 2026-07-16
**Maintained By:** George Makulu (@gmmakulu-source)
**License:** See LICENSE file
