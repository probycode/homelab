Installation

Download this repository
Save docker-compose.yml to a folder on your computer (e.g., C:\Users\YourName\Documents\my-docker-stack)
Update passwords (IMPORTANT!)
Edit docker-compose.yml and replace all default passwords:

change_this_postgres_password - PostgreSQL password
change_this_pgadmin_password - pgAdmin web interface password
change_this_n8n_password - n8n web interface password
change_this_neo4j_password - Neo4j database password


Start the stack
Open PowerShell or Command Prompt in the folder containing docker-compose.yml:

bash   docker-compose up -d

Check status

bash   docker-compose ps
Data Storage Location
All data will be stored in your Windows home directory:
C:\Users\YourUsername\docker-stack\
├── postgres\           # PostgreSQL database files
├── pgadmin\           # pgAdmin configuration
├── n8n\               # n8n workflows and settings
├── n8n-files\         # n8n file storage
├── neo4j-data\        # Neo4j database
├── neo4j-logs\        # Neo4j logs
├── neo4j-import\      # Neo4j import directory
└── neo4j-plugins\     # Neo4j plugins (APOC)
Docker will automatically create these folders when you first start the stack.
🌐 Access URLs
Once running, access the services at:
ServiceURLDefault CredentialspgAdminhttp://localhost:5050Email: admin@example.comPassword: (your configured password)n8nhttp://localhost:5678Username: adminPassword: (your configured password)Neo4j Browserhttp://localhost:7474Username: neo4jPassword: (your configured password)PostgreSQLlocalhost:5432Username: dbuserPassword: (your configured password)Neo4j Boltbolt://localhost:7687Username: neo4jPassword: (your configured password)
⚙️ Configuration
PostgreSQL
The PostgreSQL service is configured with:

Default database: postgres
Default user: dbuser
Data persistence in %USERPROFILE%\docker-stack\postgres

To create the n8n database (required for n8n):
bashdocker exec -it postgres psql -U dbuser -c "CREATE DATABASE n8n;"
Or use pgAdmin's Query Tool to run:
sqlCREATE DATABASE n8n;
pgAdmin
To connect pgAdmin to PostgreSQL:

Open http://localhost:5050
Login with your configured credentials
Right-click "Servers" → "Register" → "Server"
General tab: Name: Local PostgreSQL
Connection tab:

Host: postgres (use the container name, not localhost)
Port: 5432
Username: dbuser
Password: (your postgres password)
Save password: ✓ (optional)



n8n
n8n is configured to:

Use PostgreSQL for data persistence
Run on port 5678
Use basic authentication (username/password)
Store workflows in %USERPROFILE%\docker-stack\n8n

Important First Steps:

Create the n8n database in PostgreSQL (see PostgreSQL section above)
Restart n8n after creating the database: docker-compose restart n8n
Access http://localhost:5678 and login

Optional SMTP Configuration:
If you want n8n to send emails (for user invites, password resets), uncomment the SMTP section in docker-compose.yml and configure with your email provider's settings.
Neo4j
Neo4j is configured with:

APOC plugin pre-enabled
Data stored in %USERPROFILE%\docker-stack\neo4j-data
Neo4j Browser on port 7474
Bolt protocol on port 7687

First Login:

Open http://localhost:7474
Connect URL: bolt://localhost:7687
Username: neo4j
Password: (your configured password)

Using the Import Directory:
Place CSV or JSON files in C:\Users\YourUsername\docker-stack\neo4j-import\ to import them into Neo4j using Cypher queries.
