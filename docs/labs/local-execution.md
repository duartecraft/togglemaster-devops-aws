# 🧪 Laboratório local

> Entregável do vídeo: *"Apresentação da aplicação rodando localmente."*

## Pré-requisitos

- Docker Desktop (Windows/macOS) ou Docker Engine (Linux), com o motor **rodando**
- Git
- PowerShell, curl, Postman ou Insomnia

## Execução

Use o fork com os ajustes para a nuvem:

```bash
git clone https://github.com/duartecraft/toggle-master-monolith.git
cd toggle-master-monolith
docker compose up --build
```

Resultado esperado no log: `Tabela 'flags' inicializada com sucesso.` e `Listening at: http://0.0.0.0:5000`.

![docker compose up](../evidence/aws/01-docker-compose-local.png)

## Validação

**Linux/macOS (curl):**

```bash
curl http://localhost:5000/health
curl -X POST -H "Content-Type: application/json" -d '{"name": "new-feature", "is_enabled": true}' http://localhost:5000/flags
curl http://localhost:5000/flags
curl -X PUT -H "Content-Type: application/json" -d '{"is_enabled": false}' http://localhost:5000/flags/new-feature
curl http://localhost:5000/flags/new-feature
```

**Windows (PowerShell):** no PowerShell, `curl` é um apelido de outro comando, então use `Invoke-RestMethod`:

```powershell
Invoke-RestMethod http://localhost:5000/health
Invoke-RestMethod -Method Post -Uri http://localhost:5000/flags -ContentType "application/json" -Body '{"name": "new-feature", "is_enabled": true}'
Invoke-RestMethod http://localhost:5000/flags
Invoke-RestMethod -Method Put -Uri http://localhost:5000/flags/new-feature -ContentType "application/json" -Body '{"is_enabled": false}'
Invoke-RestMethod http://localhost:5000/flags/new-feature
```

![Testes locais](../evidence/aws/02-testes-api-local.png)

## Problemas encontrados

| Erro | Causa | Solução |
|---|---|---|
| `failed to connect to the docker API ... dockerDesktopLinuxEngine` | Docker Desktop fechado | Abrir o Docker Desktop e esperar "Engine running" |
| `./entrypoint.sh: 2: set: Illegal option -` | O Git do Windows converteu o script para CRLF | Converter para LF e criar `.gitattributes` com `*.sh text eol=lf` (já no fork) |
| `Impossível conectar-se ao servidor remoto` | O contêiner da app caiu (erro acima) | Ver `docker compose logs app` |
| `A flag 'new-feature' já existe` | O volume do PostgreSQL guarda dados de execuções anteriores | Usar outro nome ou `docker compose down -v` |

## Encerrar

```bash
docker compose down
```
