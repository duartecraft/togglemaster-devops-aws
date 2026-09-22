# 🧪 Laboratório local

## Pré-requisitos

- Docker Desktop ou Docker Engine.
- Docker Compose.
- Git.
- curl, Postman ou Insomnia.

## Execução

```bash
git clone https://github.com/dougls/toggle-master-monolith.git
cd toggle-master-monolith
docker compose up --build
```

## Validação

```bash
curl http://localhost:5000/health
curl http://localhost:5000/flags
```

## Evidências

Registrar comandos, respostas e problemas encontrados. Não registrar senhas ou arquivos `.env`.

## Commit da fase

```bash
git add .
git commit -m "feat: run ToggleMaster locally"
```
