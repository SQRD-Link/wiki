# n8n MCP Homelab

## Overzicht
Deze gids beschrijft hoe je een MCP (Model Context Protocol) server in Portainer kunt draaien om n8n workflows te laten communiceren met je Obsidian vault.

## Vereisten
- Docker en Docker Compose geïnstalleerd
- Portainer geïnstalleerd
- Obsidian met de Local REST API plugin
- n8n geïnstalleerd (Docker of standalone)

## Stap 1: Obsidian Local REST API Plugin installeren

1. Open Obsidian
2. Ga naar Settings (tandwiel linksboven)
3. Selecteer "Community Plugins" in de linker zijbalk
4. Zoek naar "Local REST API" en installeer deze
5. Activeer de plugin
6. Ga naar plugin instellingen en kopieer je API Key

## Stap 2: MCP Obsidian Server in Portainer

### Docker Compose YAML voor Portainer

```yaml
version: '3.8'
services:
  mcp-obsidian:
    image: ghcr.io/kmackett/mcp-obsidian-docker:latest
    container_name: mcp-obsidian
    environment:
      - OBSIDIAN_API_KEY=jouw_obsidian_api_key_hier
    ports:
      - "27124:27124"
    volumes:
      - ./logs:/app/logs
    restart: unless-stopped
    networks:
      - homelab

networks:
  homelab:
    external: true
```

### Stappen in Portainer:
1. Ga naar Stacks
2. Klik op "Add Stack"
3. Geef de stack een naam: `mcp-obsidian`
4. Plak bovenstaande YAML code
5. Vervang `jouw_obsidian_api_key_hier` met je echte API key
6. Deploy de stack

## Stap 3: n8n MCP Client Node installeren

### In n8n:
1. Ga naar Settings → Community Nodes
2. Installeer: `@nerding-io/n8n-nodes-mcp`
3. Herstart n8n

### Environment Variable voor AI Agent ondersteuning:
```bash
N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
```

### Docker Compose voor n8n met MCP ondersteuning:

```yaml
version: '3.8'
services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    environment:
      - N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
      - MCP_OBSIDIAN_API_KEY=jouw_obsidian_api_key_hier
    ports:
      - "5678:5678"
    volumes:
      - ~/.n8n:/home/node/.n8n
    restart: unless-stopped
    networks:
      - homelab

networks:
  homelab:
    external: true
```

## Stap 4: MCP Client configureren in n8n

### Credentials instellen:

1. **Voor HTTP Streamable (aanbevolen):**
   - Type: MCP Client (HTTP Streamable) API
   - HTTP Streamable URL: `http://mcp-obsidian:27124/stream`
   - Headers (indien nodig): `Authorization: Bearer jouw_api_key`

2. **Voor Command Line (alternatief):**
   - Command: `npx`
   - Arguments: `-y @modelcontextprotocol/server-obsidian`
   - Environment Variables: `OBSIDIAN_API_KEY=jouw_api_key`

## Stap 5: n8n Workflow voorbeelden

### Basis Obsidian integratie workflow:

```json
{
  "name": "Obsidian MCP Test",
  "nodes": [
    {
      "parameters": {},
      "id": "start",
      "name": "When clicking 'Test workflow'",
      "type": "n8n-nodes-base.manualTrigger",
      "position": [240, 300],
      "typeVersion": 1
    },
    {
      "parameters": {
        "operation": "listTools",
        "credentials": "mcp-obsidian-creds"
      },
      "id": "list-tools",
      "name": "List Obsidian Tools",
      "type": "@nerding-io/n8n-nodes-mcp.mcpClient",
      "position": [460, 300],
      "typeVersion": 1
    },
    {
      "parameters": {
        "operation": "executeTool",
        "toolName": "create_note",
        "toolParameters": {
          "title": "Test Note from n8n",
          "content": "Deze notitie is automatisch gemaakt door n8n via MCP"
        },
        "credentials": "mcp-obsidian-creds"
      },
      "id": "create-note",
      "name": "Create Note",
      "type": "@nerding-io/n8n-nodes-mcp.mcpClient",
      "position": [680, 300],
      "typeVersion": 1
    }
  ],
  "connections": {
    "When clicking 'Test workflow'": {
      "main": [
        [
          {
            "node": "List Obsidian Tools",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "List Obsidian Tools": {
      "main": [
        [
          {
            "node": "Create Note",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}
```

### AI Agent met Obsidian integratie:

```json
{
  "name": "AI Agent met Obsidian",
  "nodes": [
    {
      "parameters": {
        "options": {}
      },
      "id": "chat-trigger",
      "name": "Chat Trigger",
      "type": "n8n-nodes-base.chatTrigger",
      "position": [240, 300],
      "typeVersion": 1
    },
    {
      "parameters": {
        "agent": "conversationalAgent",
        "promptType": "define",
        "text": "Je bent een AI assistent die kan helpen met het beheren van een Obsidian vault. Je kunt notities maken, zoeken, en bewerken. Gebruik de beschikbare tools om gebruikers te helpen.",
        "hasOutputParser": false,
        "options": {
          "systemMessage": "Je hebt toegang tot Obsidian tools via MCP. Gebruik deze om notities te maken, bewerken en zoeken wanneer de gebruiker daarom vraagt."
        },
        "tools": [
          {
            "toolType": "communityPackageTool",
            "packageName": "@nerding-io/n8n-nodes-mcp",
            "toolName": "mcpClient"
          }
        ]
      },
      "id": "ai-agent",
      "name": "AI Agent",
      "type": "n8n-nodes-base.agent",
      "position": [460, 300],
      "typeVersion": 1
    }
  ],
  "connections": {
    "Chat Trigger": {
      "main": [
        [
          {
            "node": "AI Agent",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}
```

## Beschikbare Obsidian MCP Tools

De MCP Obsidian server biedt verschillende tools voor interactie met je vault via de Local REST API:

- `create_note` - Nieuwe notitie maken
- `update_note` - Bestaande notitie bijwerken
- `delete_note` - Notitie verwijderen
- `search_notes` - Zoeken in vault
- `list_notes` - Alle notities weergeven
- `get_note` - Specifieke notitie ophalen

## Troubleshooting

### Veelvoorkomende problemen:

1. **Authentication Failed:**
   - Controleer of je API key correct is in zowel Obsidian als je environment variabele

2. **Connection Refused:**
   - Zorg dat Obsidian draait en de Local REST API plugin is geactiveerd
   - Controleer of port 27124 open is

3. **Port Conflict:**
   - Als port 27124 al in gebruik is, wijzig de port mapping in docker-compose.yml

4. **n8n kan MCP node niet gebruiken als tool:**
   - Zorg dat `N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true` is ingesteld

### Logs checken:

```bash
# MCP server logs
docker logs mcp-obsidian

# n8n logs
docker logs n8n
```

## Geavanceerde configuratie

### Meerdere MCP servers:

Je kunt meerdere MCP servers tegelijk gebruiken voor verschillende functionaliteiten:

```yaml
version: '3.8'
services:
  mcp-obsidian:
    image: ghcr.io/kmackett/mcp-obsidian-docker:latest
    environment:
      - OBSIDIAN_API_KEY=jouw_api_key
    ports:
      - "27124:27124"
    
  mcp-brave-search:
    image: mcp/brave-search:latest
    environment:
      - BRAVE_API_KEY=jouw_brave_key
    ports:
      - "27125:27124"
    
  n8n:
    image: n8nio/n8n:latest
    environment:
      - N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
      - MCP_OBSIDIAN_API_KEY=jouw_obsidian_key
      - MCP_BRAVE_API_KEY=jouw_brave_key
    ports:
      - "5678:5678"
    depends_on:
      - mcp-obsidian
      - mcp-brave-search

networks:
  homelab:
    external: true
```

## Nuttige resources

- n8n MCP Client Node documentatie
- MCP Obsidian Docker repository
- Officiële MCP Client voor n8n

## Veiligheidsoverwegingen

1. Bewaar API keys nooit in je workflow code
2. Gebruik environment variables voor gevoelige informatie
3. Beperk netwerk toegang tot alleen noodzakelijke services
4. Regelmatig backups maken van je vault
5. Monitor logs voor ongeautoriseerde toegang

---

*Laatst bijgewerkt: September 2025*
