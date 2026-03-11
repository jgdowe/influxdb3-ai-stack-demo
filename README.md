# InfluxDB 3 AI Stack Demo

A ready-to-run Docker Compose stack with three containers:

| Container | Image | Port | Purpose |
|---|---|---|---|
| `demo-influxdb3-enterprise` | `influxdb:3-enterprise` | `18181` | Time-series database |
| `demo-influxdb3-explorer` | `influxdata/influxdb3-ui` | `18888` | Web UI for querying & managing data |
| *(MCP server)* | `influxdata/influxdb3-mcp-server` | stdio | Launched on demand by your AI client — see below |

---

## Prerequisites

- **Docker Desktop** → https://docs.docker.com/get-docker/
- An **email address** to activate a free InfluxDB 3 Enterprise trial (sent by email on first boot)

---

## Project Setup

### Option A — Git users

```bash
git clone https://github.com/YOUR_USERNAME/influxdb3-ai-stack-demo.git
cd influxdb3-ai-stack-demo
```

### Option B — No Git? Download the ZIP

1. Click the green **Code** button at the top of this page
2. Click **Download ZIP**
3. Unzip the file and open a terminal inside the `influxdb3-ai-stack-demo` folder:
   - **macOS:** right-click the folder in Finder → **New Terminal at Folder**
   - **Windows:** open the folder in File Explorer, click the address bar, type `cmd` and press Enter

---

From here, everyone follows the same three steps:

### 1 — Run the setup script

```bash
bash scripts/setup.sh
```

This creates all directories, generates a secure admin token, and wires it into `.env`, `config/admin-token.json`, and `config/config.json` automatically.

**Verify your project looks like this before continuing:**

```
influxdb3-ai-stack-demo/
├── compose.yaml
├── .env.example
├── .env                  ← created, with token pre-filled
├── .gitignore
├── README.md
├── scripts/
│   └── setup.sh
├── config/               ← created
│   ├── admin-token.json  ← pre-generated admin token
│   └── config.json       ← Explorer pre-connection config
└── data/                 ← created
    ├── enterprise/
    ├── plugins/
    └── explorer/
```

### 2 — Add your email to `.env`

Open `.env` in any text editor — this is the **only value you need to fill in**:

```dotenv
INFLUXDB3_ENTERPRISE_LICENSE_EMAIL=your-email@example.com
```

### 3 — Start the stack

```bash
docker compose up -d
```

Go to http://localhost:18888 — Explorer opens pre-connected to your InfluxDB instance. ✅

> On first boot, InfluxDB will send a verification email to the address you provided. Click the link to activate your trial license. The server will complete initialization automatically.

---

## Connect to Claude Desktop

The MCP server lets Claude Desktop query and manage your InfluxDB instance directly using natural language.

1. Open **Claude Desktop**
2. Go to **Settings** → **Developer** → **Edit Config**
3. Open the `claude_desktop_config.json` file with a text editor
4. Inside the `mcpServers` block, add the `influxdb` entry below — leave any existing servers untouched (replace `YOUR_ADMIN_TOKEN` with the value of `INFLUXDB3_AUTH_TOKEN` from your `.env`):

```json
{
  "mcpServers": {
    "...any existing servers stay here...": {},
    "influxdb_ai_demo": {
      "command": "docker",
      "args": [
        "run", "--rm", "--interactive",
        "--add-host=host.docker.internal:host-gateway",
        "--env", "INFLUX_DB_PRODUCT_TYPE",
        "--env", "INFLUX_DB_INSTANCE_URL",
        "--env", "INFLUX_DB_TOKEN",
        "influxdata/influxdb3-mcp-server"
      ],
      "env": {
        "INFLUX_DB_PRODUCT_TYPE": "enterprise",
        "INFLUX_DB_INSTANCE_URL": "http://host.docker.internal:18181/",
        "INFLUX_DB_TOKEN": "YOUR_ADMIN_TOKEN"
      }
    }
  }
}
```

5. Save the file and restart Claude Desktop.

🚀 You're all set — start building your InfluxDB 3 AI use case!

---

## Reference

### Ports Used

| Service | Host Port | Container Port |
|---|---|---|
| InfluxDB 3 Enterprise API | `18181` | `8181` |
| InfluxDB 3 Explorer UI | `18888` | `80` |

### Useful Commands

```bash
# View logs
docker compose logs -f

# View logs for one service
docker compose logs -f demo-influxdb3-enterprise

# Stop everything (data is preserved)
docker compose down

# Stop and wipe all data
docker compose down -v

# Restart one service
docker compose restart demo-influxdb3-enterprise

# Open a shell in the Enterprise container
docker exec -it demo-influxdb3-enterprise bash

# Run a SQL query
docker exec demo-influxdb3-enterprise \
  influxdb3 query --token YOUR_TOKEN --database YOUR_DB \
  "SELECT * FROM my_table LIMIT 10"

# Write a test data point
docker exec demo-influxdb3-enterprise \
  influxdb3 write --token YOUR_TOKEN --database YOUR_DB \
  "cpu,host=server01 usage=42.5"
```

### Troubleshooting

**Container exits immediately on first boot**  
→ Make sure `INFLUXDB3_ENTERPRISE_LICENSE_EMAIL` is set in `.env`  
→ Check your inbox for a verification email from InfluxData and click the link  
→ View logs: `docker compose logs demo-influxdb3-enterprise`

**Explorer shows a connection error**  
→ Confirm InfluxDB Enterprise is healthy: `docker compose ps`  
→ Check the token in `config/config.json` matches `INFLUXDB3_AUTH_TOKEN` in `.env`

**MCP server not connecting in Claude Desktop**  
→ Make sure the token in `claude_desktop_config.json` matches `INFLUXDB3_AUTH_TOKEN` in `.env`  
→ Restart Claude Desktop after editing the config file

**Port already in use**  
→ Edit the host ports in `compose.yaml` (left side of the `ports` mapping)

### Resources

- [InfluxDB 3 Enterprise Docs](https://docs.influxdata.com/influxdb3/enterprise/)
- [InfluxDB 3 Explorer Docs](https://docs.influxdata.com/influxdb3/explorer/)
- [Use the InfluxDB 3 MCP server](https://docs.influxdata.com/influxdb3/enterprise/admin/mcp-server/)