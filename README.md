# Spring AI MCP Weather Server

A lightweight [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) server built with Spring Boot and Spring AI. This server exposes real-time weather capabilities to AI clients (like Claude Desktop) through standard input/output (STDIO) transport.

## Features
- **MCP Compatibility**: Fully compliant with the official Model Context Protocol interface.
- **STDIO Transport**: Designed to run seamlessly as a local subprocess for isolated and secure execution.
- **Spring AI Powered**: Built efficiently using the `spring-ai-starter-mcp-server` library.

## Prerequisites
- **Java 17** or higher
- **Maven** (A Maven wrapper is included)
- **Claude Desktop** (for testing as an MCP Client)

## Getting Started

### 1. Build the Project
Compile the project and generate the executable JAR file.

```bash
# On Windows
.\mvnw.cmd clean package -DskipTests

# On macOS/Linux
./mvnw clean package -DskipTests
```
The build output will be located at `target/weatherDemo-0.0.1-SNAPSHOT.jar`.

---

## Integration with Claude Desktop

To test this server locally using Claude Desktop as the MCP client, you need to point Claude's configuration to your generated JAR.

1. Locate your Claude Desktop configuration file. On Windows, it is typically at:
   `%APPDATA%\Local\Packages\Claude\LocalCache\Roaming\Claude\claude_desktop_config.json`
   *(On Mac, it is typically `~/Library/Application Support/Claude/claude_desktop_config.json`)*

2. Add the `mcpServers` object to the configuration file. Replace the `-jar` absolute file path with the actual location on your system:

```json
{
  "mcpServers": {
    "spring-ai-mcp-weather": {
      "command": "java",
      "args": [
        "-Dspring.ai.mcp.server.transport=STDIO",
        "-jar",
        "C:\\ABSOLUTE\\PATH\\TO\\weatherDemo\\target\\weatherDemo-0.0.1-SNAPSHOT.jar"
      ]
    }
  }
}
```

3. **Restart Claude Desktop**. It will silently spin up the Java subprocess and connect securely via STDIO.

## Usage
Once configured, you can simply ask Claude in a regular prompt:
> *"What is the weather like in Seattle (latitude: 47.6062, longitude: -122.3321)?"*

Claude will automatically leverage your Spring Application context, invoking the weather tooling locally before generating an answer.

## Troubleshooting

- **`UnsupportedClassVersionError`**: Ensure your system's default `java` command matches the compiled Java release version (currently `<java.version>17</java.version>`). If Claude uses a different Java version on your system path, you can specify the absolute path to your `java.exe` in the `"command"` parameter of `claude_desktop_config.json`.
- **JSON Decoding Errors in Claude**: MCP STDIO transport requires standard output to only contain JSON. If you add `System.out.println()`, the protocol will break. Spring Boot console logs are turned off (`logging.level.root=off`) by default in `application.properties` to ensure integrity.

---
