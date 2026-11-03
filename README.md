# orchestra-mcp

Servidor [MCP](https://modelcontextprotocol.io) para [Orchestra](https://ba-lcdlc-2026-orchestra.web.app):
un agente de IA (Claude Desktop, Cursor, …) puede programar el timeline, ajustar las variables de
cada experiencia, mover la librería, gestionar dispositivos, tiendas y alertas — con los permisos
REALES del usuario dueño de la clave (viewer/editor/admin).

Un único archivo `cli.mjs` autocontenido. Requiere Node 20+.

## 1. Sacar una API key

Orchestra → **Ajustes → «Agentes (MCP)» → Tus claves de API** → Crear clave → copiar el token `okr_…`
(se muestra UNA sola vez). La clave actúa con tu rol y tu ámbito, y se puede revocar en cualquier
momento desde ese mismo panel.

## 2. Configurar el agente

Claude Desktop — `~/Library/Application Support/Claude/claude_desktop_config.json`
Cursor — `~/.cursor/mcp.json` (mismo bloque):

```json
{
  "mcpServers": {
    "orchestra": {
      "command": "npx",
      "args": [
        "-y",
        "github:Bagel-Affairs/orchestra-mcp",
        "--base-url=https://ba-lcdlc-2026-orchestra.web.app",
        "--api-key=okr_…"
      ]
    }
  }
}
```

Reinicia el agente. Verás solo las herramientas que tu rol permite.

> También vale `npx -y @bagel-affairs/orchestra-mcp` si el paquete está publicado en npm.

## 3. Opciones

| Flag | Env | Defecto |
|---|---|---|
| `--base-url=…` | `ORCHESTRA_BASE_URL` | `http://localhost:3000` |
| `--api-key=okr_…` | `ORCHESTRA_API_KEY` | — (obligatoria) |
| `--transport=stdio\|http` | `ORCHESTRA_TRANSPORT` | `stdio` |
| `--port=…` | `ORCHESTRA_PORT` | `8790` (solo http) |

En modo `http` el servidor escucha Streamable MCP en `POST /mcp` (útil para compartir el proceso
entre varios clientes de una misma máquina; la clave sigue yendo en `--api-key`).

## 4. Probar sin agente

```bash
node cli.mjs --base-url=https://… --api-key=okr_…
# y en otro terminal, un cliente MCP mínimo; o simplemente conéctalo al agente y pregunta
# «¿qué tiendas hay?» — devices/shops no requieren rol editor.
```

## Seguridad

- La clave viaja como `Authorization: Bearer` SOLO hacia la URL de Orchestra que tú configuras.
- No hay tools de facturación, borrado de organización ni suplantación de cuenta.
- Cada acción del agente queda auditada en Orchestra marcada como hecha por API key.
- Revocar: borrar la clave en Ajustes (efecto inmediato) o desactivar al usuario.
