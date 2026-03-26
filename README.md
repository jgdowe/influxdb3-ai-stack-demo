# The Agentic Time Series Demo Challenge


This guide walks you through setting up everything you need to build and demo an AI-powered use case with InfluxDB 3. By the end, you will have a running database, a web interface to explore your data, and Claude Desktop connected and ready to query your data using natural language.

No prior technical experience is required — just follow each step in order.

---

## WHAT YOU ARE SETTING UP

This project starts three things on your computer:

| What | What it does |
|---|---|
| **InfluxDB 3 Enterprise** | The database that stores your time-series data |
| **InfluxDB 3 Explorer** | A web interface to browse and query your data (opens in your browser) |
| **InfluxDB 3 MCP Server** | Lets Claude Desktop talk directly to your database |

Everything runs locally on your machine — nothing is sent to the cloud.

---

## PREREQUISITES

Before you start, make sure you have installed:

**1. Docker Desktop**

Docker is the software that runs the database and web interface on your computer.

1. Go to https://docs.docker.com/get-docker/
2. Download the right installer for your machine:
   - **Mac:** click **Mac with Apple Silicon** — this is the correct version for most modern Macs. If you are unsure, click the Apple  menu (top-left of your screen) → **About This Mac** and check that it says "Apple M1", "M2", "M3" or similar
   - **Windows:** click **Docker Desktop for Windows**
3. Open the downloaded file and follow the installer instructions
4. Once installed, open **Docker Desktop**
5. You will be prompted to create a free Docker account — go ahead and create one. When asked to choose a plan, select **Personal**. Use a personal email address rather than your work InfluxData email for this
6. After creating your account, **check your email inbox** — Docker will send you a verification email. Click the link in that email to verify your account. Docker Desktop may not function correctly until this is done
7. If Docker prompts you to install **Python** during or after setup, click **Yes** and let it install — this is expected
8. Once verified and signed in, leave Docker Desktop running in the background — you will see its whale icon in your taskbar (Windows) or menu bar (Mac)

**2. Claude Desktop**

- Download it here: https://claude.ai/download
- Run the installer and sign in with your Anthropic account

**3. An email address**

You will need an email address to activate a free InfluxDB 3 Enterprise trial. Any personal or work email works.

---

## PROJECT SETUP

### 1 — Get the project files

**Option A — Download as a ZIP (recommended if you are not a developer)**

1. Click the green **Code** button near the top of this GitHub page
2. Click **Download ZIP**
3. Find the downloaded ZIP file (usually in your **Downloads** folder) and unzip it:
   - **Mac:** double-click the ZIP file — it will create a folder called `influxdb3-ai-stack-demo-main`
   - **Windows:** right-click the ZIP file → **Extract All** → click **Extract**
4. Rename the folder to `influxdb3-ai-stack-demo` (remove the `-main` at the end)

**Option B — Git (for developers)**

```bash
git clone https://github.com/YOUR_USERNAME/influxdb3-ai-stack-demo.git
cd influxdb3-ai-stack-demo
```

---

From here, everyone follows the same six steps.

### 2 — Open a terminal inside the project folder

A terminal is a text-based window where you type commands. Here is how to open one directly inside the project folder:

**On Mac:**

1. Open **Finder**
2. Navigate to the `influxdb3-ai-stack-demo` folder — but do **not** open it, just click it once so it is selected (highlighted)
3. Right-click on the folder (or hold **Control** and click)
4. In the menu that appears, look for **"New Terminal at Folder"** and click it

> If you do not see "New Terminal at Folder" in the menu, go to **System Settings** → **Keyboard** → **Keyboard Shortcuts** → **Services**, scroll down to the **Files and Folders** section, and tick **New Terminal at Folder**. Then try again.

A Terminal window will open showing a line that ends with a `$` symbol. This means it is ready for you to type commands.

**On Windows:**

1. Open **File Explorer**
2. Navigate to and open the `influxdb3-ai-stack-demo` folder (double-click to go inside it — you should see files like `compose.yaml` and `README.md`)
3. Click once on the **address bar** at the very top of the File Explorer window — the path (e.g. `C:\Users\YourName\Downloads\influxdb3-ai-stack-demo`) will become highlighted in blue
4. While it is highlighted, type `cmd` (this replaces the path) and press **Enter**

A black Command Prompt window will open, already pointed at your project folder. You will see a line ending with `>` — this means it is ready.

---

### 3 — Run the setup script

In the terminal window you just opened, type the following and press **Enter**:

**Mac / Linux:**
```bash
bash scripts/setup.sh
```

**Windows:**
```cmd
bash scripts/setup.sh
```

> **Windows users:** If you see an error saying `bash` is not recognised, make sure you have **Git for Windows** installed (https://gitforwindows.org), which includes a bash terminal. Alternatively, right-click the `influxdb3-ai-stack-demo` folder and choose **"Open Git Bash here"** instead of using Command Prompt.

This script automatically:
- Creates all the folders the project needs
- Generates a secure access token for your database
- Saves everything into the right configuration files

You do not need to understand what it does — just run it and wait for it to finish.

**After it finishes, your project folder should contain these files and folders:**

```
influxdb3-ai-stack-demo/
├── compose.yaml
├── .env                  ← created automatically (you will edit this next)
├── .env.example
├── .gitignore
├── README.md
├── scripts/
│   └── setup.sh
├── config/               ← created automatically
│   ├── admin-token.json
│   └── config.json
└── data/                 ← created automatically
    ├── enterprise/
    ├── plugins/
    └── explorer/
```

> **Note:** Files starting with a dot (like `.env`) are hidden by default and may not be visible in your folder. To show them:
>
> **Mac:** Open Finder, navigate to the project folder, then press **Command + Shift + .** (the period key). Hidden files will appear in grey. Press the same shortcut again to hide them.
>
> **Windows:** Open File Explorer, click the **View** tab at the top of the window, then tick the **Hidden items** checkbox.

---

### 4 — Add your email to the `.env` file

The `.env` file holds your project settings. You need to add your email address so InfluxDB can activate your free trial licence.

**How to open `.env`:**

**Mac:**
1. Open **Finder** and navigate to the `influxdb3-ai-stack-demo` folder
2. Press **Command + Shift + .** to show hidden files
3. Right-click on the `.env` file → **Open With** → **TextEdit**

**Windows:**
1. Open **File Explorer** and navigate to the `influxdb3-ai-stack-demo` folder
2. Make sure **Hidden items** is ticked under the **View** tab
3. Right-click on the `.env` file → **Open With** → choose **Notepad**

**What to change:**

Find this line near the top of the file:

```
INFLUXDB3_ENTERPRISE_LICENSE_EMAIL=
```

Type your email address directly after the `=` sign, with no spaces:

```
INFLUXDB3_ENTERPRISE_LICENSE_EMAIL=your-email@example.com
```

This is the **only line you need to change**. Everything else was filled in automatically by the setup script.

Save the file (**Command + S** on Mac, **Ctrl + S** on Windows) and close the text editor.

---

### 5 — Start the stack

Go back to your terminal window and type the following, then press **Enter**:

```
docker compose up -d
```

This tells Docker to start the database and web interface. The first time you run this, Docker will download the necessary software — this may take a few minutes depending on your internet speed. You will see text scrolling past — this is normal.

When it finishes and you see a new line with `$` or `>` ready for input, open your web browser and visit:

**http://localhost:18888**

You should see the InfluxDB Explorer interface, already connected to your database. ✅

> ⚠️ **Important — you must verify your licence before the database will work:**
>
> 1. Check the inbox of the email address you added to `.env` in Step 4
> 2. Look for an email from **InfluxData** with subject line about licence activation or trial confirmation
> 3. Click the verification link inside that email
> 4. Come back and refresh http://localhost:18888
>
> The database will not finish starting up until you click that link. If Explorer shows an error or a blank screen, this is almost always the reason — check your inbox (and spam folder) first.

---

### 6 — Connect Claude Desktop to your database

This step lets Claude Desktop talk directly to your InfluxDB database so you can explore and interact with your data using natural language.

**a. Find your admin token**

Open the `.env` file again (same steps as in Step 4).

Find the line that starts with:

```
INFLUXDB3_AUTH_TOKEN=
```

Select and copy everything after the `=` sign — this long string of characters is your admin token. Keep it somewhere handy (a temporary note is fine).

**b. Open the Claude Desktop config file**

1. Open **Claude Desktop**
2. Find and click your **account name** in the **bottom-left corner** of the app window
3. Click **Settings** in the menu that appears
4. Go to the **Developer** tab
5. Click **Edit Config**

This will open a file called `claude_desktop_config.json` in your default text editor.

**c. Add the InfluxDB connection**

The file will look something like this — the `"preferences"` section may be empty or have some content in it:

```json
{
  "preferences": {}
}
```

Add the `"mcpServers"` block after the `"preferences"` section. The result should look like this — replace `YOUR_ADMIN_TOKEN` with the token you copied in step a:

```json
{
  "preferences": {},
  "mcpServers": {
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

> **Tip:** Make sure there is a comma after the closing `}` of the `"preferences"` block — as shown above. If the comma is missing, Claude Desktop will not be able to read the file.

**d. Save and restart Claude Desktop**

Save the file, then fully quit Claude Desktop and open it again.

🚀 You are all set — Claude Desktop can now query your InfluxDB database directly!

---

## BUILDING YOUR USE CASE

### 1 — Ingest your data

Your team is responsible for getting data into InfluxDB that fits your scenario. There are two common approaches:

**One-off load** — if you already have a dataset (a CSV file, for example), you can load it into InfluxDB once. Claude Desktop can help you write a script to do this — just describe your data and ask.

**Simulated real-time data** — if your scenario involves live or continuously updating data (for example, sensor readings, application metrics, or financial prices), a simple Python script can generate and send data to InfluxDB on a loop. Claude Desktop can write this script for you — just describe what kind of data your scenario needs.

Once your stack is running, you can also ask Claude Desktop directly to create databases or write data on your behalf.

### 2 — Build a UI with Claude

Claude can help you build a way to present or interact with your data. Depending on what your demo needs:

**Claude Artifacts** — the simplest option. Just ask Claude Desktop to build a chart or dashboard, and it will generate one directly inside the chat window. No files or extra setup needed — great for quick demos.

**Static web page** — ask Claude to generate a single HTML file that displays your data in a browser. You can open it directly on your computer or share it with others. No technical setup required.

**PowerPoint presentation** — ask Claude to generate a slide deck summarising your data, findings, or scenario story. A good option if you are presenting to stakeholders rather than running a live demo.

**Claude Code** — if your team needs a more complete application (multiple pages, a backend, more complex logic), Claude Code can help build it. This is the most advanced option and works best for teams with some development experience.

---

## REFERENCE

### Useful commands

Run these in your terminal from inside the `influxdb3-ai-stack-demo` folder:

```bash
# Check what is running
docker compose ps

# View live logs (press Ctrl+C to stop)
docker compose logs -f

# Stop everything (your data is preserved)
docker compose down

# Start everything again
docker compose up -d
```

### Troubleshooting

**The database stops immediately after starting**
→ Make sure you added your email to the `INFLUXDB3_ENTERPRISE_LICENSE_EMAIL` line in `.env`
→ Check your inbox for a verification email from InfluxData and click the verification link
→ Run `docker compose logs demo-influxdb3-enterprise` in your terminal to see what went wrong

**Explorer shows a connection error at http://localhost:18888**
→ Wait 30 seconds and refresh the page — the database may still be starting up
→ Run `docker compose ps` in your terminal to check that both services show as **running**

**Claude Desktop cannot connect to the database**
→ Make sure the token in `claude_desktop_config.json` exactly matches the `INFLUXDB3_AUTH_TOKEN` value in your `.env` file
→ Make sure Docker Desktop is open and running in the background
→ Fully quit and reopen Claude Desktop after making any changes to the config file

**"Port already in use" error when starting**
→ Something else on your computer is already using port 18181 or 18888
→ Ask a teammate for help changing the port numbers in `compose.yaml`

### Resources

- [InfluxDB 3 Enterprise Docs](https://docs.influxdata.com/influxdb3/enterprise/)
- [InfluxDB 3 Explorer Docs](https://docs.influxdata.com/influxdb3/explorer/)
- [Use the InfluxDB 3 MCP server](https://docs.influxdata.com/influxdb3/enterprise/admin/mcp-server/)
