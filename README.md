# Visagismo Greco \u2014 Vitrine p\u00fablica

P\u00e1gina p\u00fablica de **an\u00e1lise facial com IA** da Greco Barbearia. Frontend
est\u00e1tico (HTML/CSS/JS) que conversa com o backend Visagismo dentro do
**Greco Metas** (`https://www.grecopro.com.br`).

## Como funciona

1. Cliente faz upload da foto (ou usa c\u00e2mera).
2. Frontend envia `POST /api/visagismo/analyze` para o Greco Metas.
3. Backend chama Claude Sonnet (via chave armazenada criptografada),
   identifica o formato do rosto, sugere 3 cortes e dispara em background
   a gera\u00e7\u00e3o das imagens via Fal.ai.
4. Frontend faz polling em `GET /api/visagismo/session/:uuid` at\u00e9 as
   imagens carregarem.
5. Ao final, aparece o CTA \u201cQuer agendar?\u201d. Se o cliente preencher
   nome + WhatsApp, chamamos `POST /api/visagismo/lead`, que:
   - cruza o telefone com `recClients` no Greco Metas;
   - se for cliente existente \u2192 atualiza ficha (`recClientProfiles`,
     tags, notas) e dispara Telegram no canal de opera\u00e7\u00f5es;
   - se for lead novo \u2192 grava como lead e notifica Telegram no canal de
     leads/SDR.

## Configura\u00e7\u00e3o

O endpoint padr\u00e3o \u00e9 `https://www.grecopro.com.br`. Para apontar para
outro ambiente (staging, local), defina antes do `<script>`:

```html
<script>
  window.VISAGISMO_API_BASE = 'https://staging.grecopro.com.br';
  window.VISAGISMO_PUBLIC_TOKEN = '...'; // se backend exigir x-visagismo-token
</script>
```

UTMs s\u00e3o capturados automaticamente da query string (`utm_source`,
`utm_medium`, `utm_campaign`, `utm_term`, `utm_content`, `ref`) e
persistidos em `localStorage` para sobreviver entre p\u00e1ginas.

## Deploy no Railway

O reposit\u00f3rio est\u00e1 configurado para servir como site est\u00e1tico via
Caddy (`Caddyfile` + `railway.json`). O Railway:

1. detecta Nixpacks;
2. instala o Caddy;
3. roda `caddy run --config Caddyfile --adapter caddyfile`.

Caddy ouve na porta `$PORT` que o Railway injeta.

Dom\u00ednio em produ\u00e7\u00e3o: `https://visagismo.greco.com.br`.

## Desenvolvimento local

Qualquer servidor est\u00e1tico funciona, por exemplo:

```bash
python3 -m http.server 8080
```

Lembre de ajustar `window.VISAGISMO_API_BASE` se quiser apontar para o
backend rodando localmente.

## Estrutura

```
.
\u251c\u2500 index.html       # vitrine (\u00fanico arquivo)
\u251c\u2500 Caddyfile         # config do servidor est\u00e1tico
\u251c\u2500 railway.json      # config Railway
\u251c\u2500 Staticfile        # fallback p/ buildpack staticfile
\u251c\u2500 nixpacks.toml     # provider hint pro Nixpacks
\u2514\u2500 README.md
```
