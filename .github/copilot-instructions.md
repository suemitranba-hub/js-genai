# ⚡ Copilot Assistant Extension v1.2.3 - Log Access & Tool Guidelines

---

# 🚨🚨🚨 STOP - READ THIS FIRST 🚨🚨🚨

## ⛔ ABSOLUTELY FORBIDDEN - INSTANT FAILURE

### **YOU MUST NEVER, UNDER ANY CIRCUMSTANCES:**

1. **❌ USE TERMINAL COMMANDS TO START/STOP/RUN APPLICATIONS**
   - **NO `npm start`** - Use `copilotAssistant.start` instead
   - **NO `flutter run`** - Use `copilotAssistant.start` instead  
   - **NO `go run .`** - Use `copilotAssistant.start` instead
   - **NO `python app.py`** - Use `copilotAssistant.start` instead
   - **NO `node index.js`** - Use `copilotAssistant.start` instead
   - **NO `java -jar`** - Use `copilotAssistant.start` instead
   - **NO `mvn spring-boot:run`** - Use `copilotAssistant.start` instead

2. **❌ USE TERMINAL TO STOP/KILL APPLICATIONS**
   - **NO `pkill`** - Use `copilotAssistant.stop` instead
   - **NO `Stop-Process`** - Use `copilotAssistant.stop` instead
   - **NO `kill`** - Use `copilotAssistant.stop` instead

3. **❌ USE TERMINAL OR GREP TO SEARCH LOGS**
   - **NO `grep`** - Use `copilotAssistant.searchLogs` instead
   - **NO `Select-String`** - Use `copilotAssistant.searchLogs` instead
   - **NO `Get-Content`** - Use `copilotAssistant.searchLogs` instead

### **⚠️ CRITICAL CONSEQUENCE:**
If you use terminal commands for app control, **logs will NOT be captured**. You will be unable to analyze errors, debug issues, or help the user. The entire purpose of this extension is defeated.

### **✅ ALWAYS USE THESE EXTENSION COMMANDS:**
- **Start app**: `run_vscode_command({ commandId: "copilotAssistant.start" })`
- **Stop app**: `run_vscode_command({ commandId: "copilotAssistant.stop" })`
- **Restart app**: `run_vscode_command({ commandId: "copilotAssistant.restart" })`
- **Search logs**: `run_vscode_command({ commandId: "copilotAssistant.searchLogs", args: ["pattern"] })`

### **💡 WHEN USER SAYS ANY OF THESE:**
- "start the app" / "run the app" / "launch the app"
- "run the server" / "start the server"
- "boot up" / "fire up" / "spin up"
- "get it running" / "make it run"
- "check logs" / "search logs" / "find errors"

### **👉 YOU MUST USE EXTENSION COMMANDS - NOT TERMINAL**

---

## 🚨 CRITICAL RULES - NEVER BREAK THESE

### **❌ NEVER USE TERMINAL COMMANDS FOR APP CONTROL**

**FORBIDDEN COMMANDS - DO NOT USE:**
```powershell
# ❌ NEVER DO THIS:
npm start
npm run dev
flutter run
go run .
python app.py
java -jar app.jar
mvn spring-boot:run
node index.js

# ❌ NEVER DO THIS EITHER:
npm stop
pkill -f "node"
Stop-Process -Name "flutter"
```

**WHY THIS IS CRITICAL:**
- Terminal commands bypass the extension's log capture system
- Logs won't appear in Copilot Assistant panel
- You won't be able to read or analyze logs
- The extension manages the application process lifecycle
- Only extension commands enable log streaming to Copilot

### **✅ ALWAYS USE EXTENSION COMMANDS FOR APP CONTROL**

**CORRECT - Use extension commands:**
```typescript
// Starting the app
run_vscode_command({
  commandId: "copilotAssistant.start",
  name: "Start the application"
})

// Stopping the app
run_vscode_command({
  commandId: "copilotAssistant.stop",
  name: "Stop the application"
})

// Restarting the app
run_vscode_command({
  commandId: "copilotAssistant.restart",
  name: "Restart the application"
})
```

**REMEMBER:** If the user asks to "start the app", "run the server", "launch the application", or similar - ALWAYS use `copilotAssistant.start` command. NEVER use terminal commands like `npm start` or `flutter run`.

---

## 🎯 Core Principle: Use The Right Tool for the Right Job

### **For Log Search Operations (ALWAYS use extension commands):**

**✅ CORRECT - Use extension search command:**
```typescript
// Search logs using the extension (writes results to file)
run_vscode_command({
  commandId: "copilotAssistant.searchLogs",
  name: "Search logs for pattern",
  args: ["ERROR"]  // Pass the search pattern as a single argument
})

// Then read the ENTIRE results file (use a large number like 10000 for endLine)
read_file({ 
  filePath: "<workspace>/.copilot-assistant/search-results.txt",
  startLine: 1, 
  endLine: 10000  // Use a large number to read all lines
})
```

**IMPORTANT:** When using `run_vscode_command`, always pass the search pattern as the FIRST element in the args array. The pattern should be a string, not wrapped in quotes within the array.

**❌ WRONG - Don't use terminal or grep_search:**
```powershell
# ❌ DON'T DO THIS:
Get-Content .copilot-assistant/app.log | Select-String "pattern"
grep_search({ query: "pattern", includePattern: ".copilot-assistant/app.log" })
```

**Why?**
- Extension command searches in-memory logs (up to 10,000 entries)
- Results written to `.copilot-assistant/search-results.txt`
- No direct log file access needed
- Faster than file-based searching
- Works with live logs that aren't written to disk

**CRITICAL:** When reading search-results.txt, ALWAYS use a large `endLine` value (like 10000) to ensure you read ALL results. Do NOT use `endLine: -1` as it may only read one line.

### **For Terminal Operations (Use run_in_terminal ONLY for non-app tasks):**

**✅ CORRECT - Use terminal ONLY for:**
- Build commands (`npm run build`, `flutter build apk`) - NOT running the app!
- Package management (`npm install`, `flutter pub get`, `pip install`)
- Git operations (`git commit`, `git status`, `git push`)
- File manipulation (`mkdir`, `cp`, `mv`, `rm`)
- AWS/Cloud operations (`aws s3 sync`, `aws cloudfront create-invalidation`)
- Database operations (`psql`, `mysql`)

**❌ ABSOLUTELY FORBIDDEN - NEVER use terminal for:**
- **Starting applications** (`npm start`, `flutter run`, `go run .`, `python app.py`)
- **Running applications** (`npm run dev`, `node index.js`, `java -jar app.jar`)
- **Stopping applications** (`pkill`, `Stop-Process`, `kill`)
- Reading/searching logs (use `copilotAssistant.searchLogs` command)
- Counting log entries (search then count results from search-results.txt)
- Analyzing file content (use `read_file` or `semantic_search`)

**WHY:** Terminal-started apps bypass the extension's log capture. You won't be able to see or analyze logs!

---

## 📋 Log Search Workflow

**Search Results File:** `.copilot-assistant/search-results.txt` (auto-generated by extension)

### How It Works:
1. Extension keeps logs in memory (up to 10,000 entries)
2. You call `copilotAssistant.searchLogs` with a query
3. Extension searches in-memory logs and writes results to `search-results.txt`
4. You read the results file to analyze matches

### Enhanced Log Format:
```
[2025-10-03T19:23:06.115Z] [ERROR] [npm] npm error Missing script | type=ConfigurationError severity=HIGH
[2025-10-03T19:23:06.120Z] [INFO] [Flutter] App started | severity=LOW
[2025-10-03T19:23:06.125Z] [WARN] [Memory] High usage | type=MemoryError severity=MEDIUM tags=performance
```

**Metadata Fields:**
- `type`: NetworkError, MemoryError, ConfigurationError, AuthenticationError, DatabaseError, FileSystemError, SyntaxError, RuntimeError
- `severity`: CRITICAL, HIGH, MEDIUM, LOW
- `tags`: performance, security, api, ui, data

---

## 📖 Common Query Examples (Copy These!)

### Searching for Patterns

**User: "check logs for errors"**
```typescript
// Step 1: Search
run_vscode_command({
  commandId: "copilotAssistant.searchLogs",
  name: "Search logs for errors",
  args: ["ERROR"]
})

// Step 2: Read ALL results (use large endLine to ensure you get everything)
read_file({
  filePath: "<workspace>/.copilot-assistant/search-results.txt",
  startLine: 1,
  endLine: 10000  // Large number ensures all lines are read
})
```

**User: "find network errors"**
```typescript
run_vscode_command({
  commandId: "copilotAssistant.searchLogs",
  name: "Search for network errors",
  args: ["type=NetworkError"]
})

read_file({
  filePath: "<workspace>/.copilot-assistant/search-results.txt",
  startLine: 1,
  endLine: 10000  // Large number ensures all lines are read
})
```

**User: "show critical issues"**
```typescript
run_vscode_command({
  commandId: "copilotAssistant.searchLogs",
  name: "Search for critical issues",
  args: ["severity=CRITICAL"]
})

read_file({
  filePath: "<workspace>/.copilot-assistant/search-results.txt",
  startLine: 1,
  endLine: 10000  // Large number ensures all lines are read
})
```

**User: "look for tight loops" or "find repeated patterns"**
```typescript
run_vscode_command({
  commandId: "copilotAssistant.searchLogs",
  name: "Search for contract update loops",
  args: ["Contracts updated"]
})

read_file({
  filePath: "<workspace>/.copilot-assistant/search-results.txt",
  startLine: 1,
  endLine: 10000  // Large number ensures all lines are read
})
```

**User: "count occurrences of X"**
```typescript
// Step 1: Search
run_vscode_command({
  commandId: "copilotAssistant.searchLogs",
  name: "Search for pattern",
  args: ["pattern"]
})

// Step 2: Read ALL results and count
read_file({
  filePath: "<workspace>/.copilot-assistant/search-results.txt",
  startLine: 1,
  endLine: 10000  // Large number ensures all lines are read
})
// Then count the lines in the result
```

### Getting Recent Logs

**User: "show recent logs"**
```typescript
// Use the getLogsForCopilot command for recent logs
run_vscode_command({
  commandId: "copilotAssistant.getLogsForCopilot",
  name: "Get recent logs"
})
// Results appear in "Copilot Assistant" output channel
```

**User: "show errors from logs"**
```typescript
run_vscode_command({
  commandId: "copilotAssistant.getErrorsForCopilot",
  name: "Get error logs"
}) - CRITICAL SECTION

### **ALWAYS Use Extension Commands for App Control**

**When users say ANY of these phrases:**
- "start the app" / "run the app" / "launch the application"
- "start the server" / "run the server" / "start development server"
- "run the code" / "execute the program" / "start the service"
- "boot up the app" / "fire up the server" / "spin up the app"
- "get the app running" / "make it run" / "start it up"

**YOU MUST execute extension command:**
```typescript
run_vscode_command({
  commandId: "copilotAssistant.start",
  name: "Start the application"
})
```

**❌ NEVER execute terminal commands like:**
```typescript
// ❌ FORBIDDEN - DO NOT DO THIS:
run_in_terminal({
  command: "npm start",
  explanation: "Starting the application",
  isBackground: true
})

// ❌ ALSO FORBIDDEN:
run_in_terminal({
  command: "flutter run",
  explanation: "Running Flutter app",
  isBackground: true
})
```

**Available Extension Command IDs:**
- `copilotAssistant.start` - Start the application (detects language automatically)
- `copilotAssistant.stop` - Stop the application
- `cApp Control Best Practices:
- **ALWAYS** use `copilotAssistant.start` to start applications
- **NEVER** use `run_in_terminal` with `npm start`, `flutter run`, etc.
- **REASON:** Only extension commands enable log capture and analysis
- **IF** you accidentally use terminal commands, logs won't be accessible
- **REMEMBER:** User wants to analyze logs with Copilot - this requires extension commands

### opilotAssistant.restart` - Restart the application (clears logs)
- `copilotAssistant.hotReload` - Trigger hot reload (Flutter/similar)

**Why This Matters:**
- Extension commands capture all app output to the log service
- Logs are searchable with `copilotAssistant.searchLogs`
- You can analyze errors with Copilot
- Terminal-started apps bypass log capture completely
- You lose all debugging capability if you use terminal commands

**Example Workflow:**
```
User: "start the app and check for errors"

1. YOU execute: run_vscode_command({ commandId: "copilotAssistant.start" })
2. YOU wait: 2-3 seconds for app to start
3. YOU execute: run_vscode_command({ commandId: "copilotAssistant.searchLogs", args: ["ERROR"] })
4. YOU execute: read_file({ filePath: "<workspace>/.copilot-assistant/search-results.txt" })
5. YOU analyze: Results and provide guidance
```

**Another Example:**
```
User: "restart the server"

1. YOU execute: run_vscode_command({ commandId: "copilotAssistant.restart" })
2. Wait for restart to complete
3. Logs are automatically captured and available for analysis

1. YOU execute: run_vscode_command({ commandId: "copilotAssistant.start" })
2. YOU wait: 2-3 seconds
3. YOU execute: run_vscode_command({ commandId: "copilotAssistant.searchLogs", args: ["ERROR"] })
4. YOU execute: read_file({ filePath: "<workspace>/.copilot-assistant/search-results.txt" })
5. YOU analyze: Results and provide guidance
```

---

## 💡 Pro Tips

### Search Best Practices:
- **Exact matches**: Search for specific log levels like "ERROR", "WARN", "INFO"
- **Metadata**: Use metadata fields like "severity=HIGH" or "type=NetworkError"
- **Partial matches**: Search works with substring matching (case-insensitive)
- **Read results**: Always read search-results.txt after searching

### Available Commands:
- `copilotAssistant.searchLogs` - Search in-memory logs, write to search-results.txt
- `copilotAssistant.getSearchResults` - Get last search results programmatically
- `copilotAssistant.getLogsForCopilot` - Get recent logs in output channel
- `copilotAssistant.getErrorsForCopilot` - Get error logs in output channel
- `copilotAssistant.getHealthForCopilot` - Get health status in output channel

### Output Channel:
- All log analysis appears in **"Copilot Assistant"** output channel (unified)
- Use notification messages to guide users to check the output panel

