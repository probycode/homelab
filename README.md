# Multi-Service Docker Stack

A comprehensive Docker Compose stack that includes PostgreSQL, pgAdmin, n8n (workflow automation), and Neo4j (graph database). Designed for Windows users running Docker Desktop.

## 📋 Services Included

- **PostgreSQL 16**: Relational database
- **pgAdmin**: Web-based PostgreSQL administration tool
- **n8n**: Workflow automation platform (similar to Zapier)
- **Neo4j**: Graph database with APOC plugin

## 🚀 Quick Start

### Prerequisites

- Windows 10/11
- Docker Desktop for Windows (with WSL2 backend recommended)
- At least 4GB RAM available
- Ports 5432, 5050, 5678, 7474, and 7687 available

### Installation

1. **Download this repository**
   
   Save `docker-compose.yml` to a folder on your computer (e.g., `C:\Users\YourName\Documents\my-docker-stack`)

2. **Update passwords** (IMPORTANT!)
   
   Edit `docker-compose.yml` and replace all default passwords:
   - `change_this_postgres_password` - PostgreSQL password
   - `change_this_pgadmin_password` - pgAdmin web interface password
   - `change_this_n8n_password` - n8n web interface password
   - `change_this_neo4j_password` - Neo4j database password

3. **Start the stack**
   
   Open PowerShell or Command Prompt in the folder containing `docker-compose.yml`:
   ```bash
   docker-compose up -d
   ```

4. **Check status**
   ```bash
   docker-compose ps
   ```

### Data Storage Location

All data will be stored in your Windows home directory:
```
C:\Users\YourUsername\docker-stack\
├── postgres\           # PostgreSQL database files
├── pgadmin\           # pgAdmin configuration
├── n8n\               # n8n workflows and settings
├── n8n-files\         # n8n file storage
├── neo4j-data\        # Neo4j database
├── neo4j-logs\        # Neo4j logs
├── neo4j-import\      # Neo4j import directory
└── neo4j-plugins\     # Neo4j plugins (APOC)
```

Docker will automatically create these folders when you first start the stack.

## 🌐 Access URLs

Once running, access the services at:

| Service | URL | Default Credentials |
|---------|-----|---------------------|
| **pgAdmin** | http://localhost:5050 | Email: `admin@example.com`<br>Password: (your configured password) |
| **n8n** | http://localhost:5678 | Username: `admin`<br>Password: (your configured password) |
| **Neo4j Browser** | http://localhost:7474 | Username: `neo4j`<br>Password: (your configured password) |
| **PostgreSQL** | localhost:5432 | Username: `dbuser`<br>Password: (your configured password) |
| **Neo4j Bolt** | bolt://localhost:7687 | Username: `neo4j`<br>Password: (your configured password) |

## ⚙️ Configuration

### PostgreSQL

The PostgreSQL service is configured with:
- Default database: `postgres`
- Default user: `dbuser`
- Data persistence in `%USERPROFILE%\docker-stack\postgres`

To create the n8n database (required for n8n):
```bash
docker exec -it postgres psql -U dbuser -c "CREATE DATABASE n8n;"
```

Or use pgAdmin's Query Tool to run:
```sql
CREATE DATABASE n8n;
```

### pgAdmin

To connect pgAdmin to PostgreSQL:
1. Open http://localhost:5050
2. Login with your configured credentials
3. Right-click "Servers" → "Register" → "Server"
4. **General tab**: Name: `Local PostgreSQL`
5. **Connection tab**:
   - Host: `postgres` (use the container name, not localhost)
   - Port: `5432`
   - Username: `dbuser`
   - Password: (your postgres password)
   - Save password: ✓ (optional)

### n8n

n8n is configured to:
- Use PostgreSQL for data persistence
- Run on port 5678
- Use basic authentication (username/password)
- Store workflows in `%USERPROFILE%\docker-stack\n8n`

**Important First Steps:**
1. Create the n8n database in PostgreSQL (see PostgreSQL section above)
2. Restart n8n after creating the database: `docker-compose restart n8n`
3. Access http://localhost:5678 and login

**Optional SMTP Configuration:**
If you want n8n to send emails (for user invites, password resets), uncomment the SMTP section in `docker-compose.yml` and configure with your email provider's settings.

### Neo4j

Neo4j is configured with:
- APOC plugin pre-enabled
- Data stored in `%USERPROFILE%\docker-stack\neo4j-data`
- Neo4j Browser on port 7474
- Bolt protocol on port 7687

**First Login:**
1. Open http://localhost:7474
2. Connect URL: `bolt://localhost:7687`
3. Username: `neo4j`
4. Password: (your configured password)

**Using the Import Directory:**
Place CSV or JSON files in `C:\Users\YourUsername\docker-stack\neo4j-import\` to import them into Neo4j using Cypher queries.

## 🛠️ Common Commands

### Start all services
```bash
docker-compose up -d
```

### Stop all services
```bash
docker-compose down
```

### Stop and remove all data (⚠️ WARNING: This deletes everything!)
```bash
docker-compose down -v
# Then manually delete C:\Users\YourUsername\docker-stack\
```

### View logs
```bash
# All services
docker-compose logs

# Specific service
docker-compose logs postgres
docker-compose logs n8n
docker-compose logs neo4j
docker-compose logs pgadmin

# Follow logs in real-time
docker-compose logs -f n8n
```

### Restart a specific service
```bash
docker-compose restart postgres
docker-compose restart n8n
docker-compose restart neo4j
```

### Access container shell
```bash
# PostgreSQL
docker exec -it postgres bash

# Neo4j
docker exec -it neo4j bash

# n8n
docker exec -it n8n sh
```

## 🔧 Troubleshooting

### Ports Already in Use

If you get "port already allocated" errors, another application is using that port. Solutions:

1. **Find what's using the port** (PowerShell as Administrator):
   ```powershell
   netstat -ano | findstr :5432
   netstat -ano | findstr :5050
   netstat -ano | findstr :5678
   netstat -ano | findstr :7474
   ```

2. **Change the port** in `docker-compose.yml`:
   ```yaml
   ports:
     - "15432:5432"  # Use port 15432 instead of 5432
   ```

### Permission Errors

If you get permission errors with volumes on Windows:
1. Make sure Docker Desktop has access to your C: drive
2. Go to Docker Desktop → Settings → Resources → File Sharing
3. Add `C:\Users` if not already present

### n8n Can't Connect to PostgreSQL

1. Make sure the n8n database exists:
   ```bash
   docker exec -it postgres psql -U dbuser -c "CREATE DATABASE n8n;"
   ```

2. Check if PostgreSQL is running:
   ```bash
   docker-compose ps postgres
   ```

3. Restart n8n:
   ```bash
   docker-compose restart n8n
   ```

### Neo4j Password Won't Change

If you've already started Neo4j once, you need to:
1. Stop all services: `docker-compose down`
2. Delete the Neo4j data: Remove `C:\Users\YourUsername\docker-stack\neo4j-data\`
3. Update the password in `docker-compose.yml`
4. Start again: `docker-compose up -d`

### Can't Access Services

1. Verify containers are running:
   ```bash
   docker-compose ps
   ```

2. Check container logs for errors:
   ```bash
   docker-compose logs [service-name]
   ```

3. Ensure Windows Firewall isn't blocking the ports

## 📊 Resource Usage

Approximate resource usage:
- PostgreSQL: 100-200 MB RAM
- pgAdmin: 100-150 MB RAM
- n8n: 150-300 MB RAM
- Neo4j: 500 MB - 2 GB RAM (depending on data size)

**Total: ~1-3 GB RAM minimum recommended**

## 🔒 Security Recommendations

For production or external access:

1. **Change all default passwords** immediately
2. **Use strong passwords** (16+ characters, mixed case, numbers, symbols)
3. **Don't expose ports publicly** without proper security measures
4. **Enable HTTPS** if accessible from the internet
5. **Regular backups** of the data directory
6. **Keep Docker images updated**:
   ```bash
   docker-compose pull
   docker-compose up -d
   ```

## 💾 Backup and Restore

### Backup Data

Simply copy the entire data directory:
```powershell
# PowerShell
Copy-Item -Recurse "$env:USERPROFILE\docker-stack" "$env:USERPROFILE\docker-stack-backup-$(Get-Date -Format 'yyyy-MM-dd')"
```

### Restore Data

1. Stop all services: `docker-compose down`
2. Replace the data directory with your backup
3. Start services: `docker-compose up -d`

### Database-Specific Backups

**PostgreSQL:**
```bash
# Backup
docker exec postgres pg_dump -U dbuser postgres > backup.sql

# Restore
docker exec -i postgres psql -U dbuser postgres < backup.sql
```

**Neo4j:**
```bash
# Backup (using Neo4j dump)
docker exec neo4j neo4j-admin database dump neo4j --to-path=/backups

# Restore
docker exec neo4j neo4j-admin database load neo4j --from-path=/backups
```

## 📝 License

This Docker Compose configuration is provided as-is under the MIT License. Individual services have their own licenses:
- PostgreSQL: PostgreSQL License
- pgAdmin: PostgreSQL License
- n8n: Sustainable Use License
- Neo4j: GPL v3 / Commercial License

## 🤝 Contributing

Feel free to submit issues, fork the repository, and create pull requests for any improvements.

## 📧 Support

For issues specific to:
- Docker Compose: Check Docker documentation
- PostgreSQL: https://www.postgresql.org/docs/
- pgAdmin: https://www.pgadmin.org/docs/
- n8n: https://docs.n8n.io/
- Neo4j: https://neo4j.com/docs/
