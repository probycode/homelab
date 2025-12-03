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
