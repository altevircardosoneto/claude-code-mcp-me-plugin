# effecti-mcp-me

Plugin que conecta o Claude ao servidor MCP da Effecti (ME), com autenticação OAuth:

- URL do MCP: `https://mcp-me.prod.effecti.com.br/mcp`
- Funciona no **Claude Code** e no **Claude Desktop** (chat e Cowork).
- O OAuth é feito pelo próprio cliente (Dynamic Client Registration / CIMD). O plugin não guarda client ID nem segredos.

## Claude Code

```bash
claude plugin marketplace add https://gitlab.effecti.com.br/effecti-team/minha-effecti/claude-code-mcp-me-plugin.git
claude plugin install effecti-mcp-me@effecti-mcp
```

Depois, dentro do Claude Code, rode `/mcp`, selecione `effecti-me` e conclua o login no navegador.
Também é possível autenticar com `claude mcp login`.

### Para o time inteiro

No `settings.json` (do projeto ou do usuário):

```json
{
  "extraKnownMarketplaces": {
    "effecti-mcp": {
      "source": {
        "source": "git",
        "url": "https://gitlab.effecti.com.br/effecti-team/minha-effecti/claude-code-mcp-me-plugin.git"
      }
    }
  },
  "enabledPlugins": {
    "effecti-mcp-me@effecti-mcp": true
  }
}
```

## Claude Desktop

O "Add marketplace" do Desktop não aceita GitLab privado. Use o zip:

1. Gere o pacote:
   ```powershell
   .\scripts\build-desktop-zip.ps1
   ```
2. No Desktop, vá em **Customize > Plugins > Upload plugin** e envie `dist/effecti-mcp-me.zip`.
3. Na aba **Connectors** do plugin, clique em **Connect** e conclua o OAuth.

Alternativa: o Owner da organização pode distribuir o plugin pela sincronização da organização.

## Requisitos do servidor MCP

Para funcionar nos dois clientes, o servidor de autorização precisa:

- Suportar DCR (`registration_endpoint`) ou CIMD.
- Aceitar os redirect URIs:
  - `https://claude.ai/api/mcp/auth_callback` (Desktop, claude.ai e Cowork).
  - `http://localhost:<qualquer porta>/callback` e `http://127.0.0.1:<qualquer porta>/callback` (Claude Code).
- Exigir PKCE S256.
- Responder 401 com `WWW-Authenticate: Bearer resource_metadata="..."`.
- Publicar `/.well-known/oauth-protected-resource` com `resource` idêntico à URL do MCP.
