# Dashboard Hidrodinâmico — CIEX

Painel web para visualização dos resultados do modelo hidrodinâmico (TELEMAC) da Lagoa dos Patos e região. Disponível em <https://hidrodinamica.ciex.org.br>.

## Funcionalidades

- Mapa interativo (Mapbox) com campos animados de **nível**, **corrente** e **vento**, controlados por uma linha do tempo
- Vetores de direção de corrente e vento, com tamanho proporcional à intensidade
- Gráficos de **nível** nas estações (previsto × observado), com tabela e histórico de previsões
- Gráficos de **vazão** nos pontos de monitoramento
- Seleção da fonte de dados: previsão, cenários e modelos experimentais

## Tecnologias

React 19 · Vite · Material UI · Mapbox GL (react-map-gl) · Recharts · Nginx · Docker

## Estrutura

```
.
├── frontend/            # Aplicação React (Vite)
│   ├── public/          # Arquivos estáticos (logos, CSVs, sensores)
│   └── src/
│       ├── components/  # Mapa, gráficos, painéis e camadas (layers/)
│       ├── layout/
│       └── pages/
├── Dockerfile           # Build do frontend + imagem Nginx
├── nginx.conf           # Servidor estático e rota /data
├── docker-compose.yml   # Dashboard + Traefik + Cloudflare Tunnel
└── Makefile             # Atalhos para o docker compose
```

## Dados

Os resultados do modelo (arquivos JSON) são lidos pela aplicação a partir de `/data`:

- **Desenvolvimento:** a pasta `data/` na raiz do repositório é servida em `/data` pelo Vite (se não existir, usa `frontend/public/data`).
- **Produção:** o volume `/mnt/docker/data` do servidor é montado no Nginx em `/data`.

## Desenvolvimento

Requisitos: Node.js 22+ e pnpm (ou npm).

Crie `frontend/.env` com o token do Mapbox:

```env
VITE_MAPBOX_TOKEN=seu_token_aqui
```

Depois:

```bash
cd frontend
pnpm install
pnpm dev        # servidor em http://localhost:5173
pnpm build      # build de produção em dist/
pnpm lint
```

## Deploy

As variáveis de produção ficam em `frontend/.env.production` (incluindo `VITE_MAPBOX_TOKEN` e `CLOUDFLARE_TUNNEL_TOKEN`). A rede Docker `proxy` precisa existir (`docker network create proxy`).

```bash
make build     # gera a imagem
make up        # sobe os containers
make logs      # acompanha os logs
make restart   # reinicia
make down      # derruba
```
