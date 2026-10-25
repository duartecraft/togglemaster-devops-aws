# 🗺️ Roadmap do projeto

## Fase 1 — Execução local

- Clonar o projeto-base.
- Executar com Docker Compose.
- Validar `/health` e CRUD de flags.
- Documentar a estrutura monolítica.
- Commit: `feat: run ToggleMaster locally`.

## Fase 2 — Arquitetura AWS

- Definir VPC e CIDRs.
- Separar subnets pública e privada.
- Definir EC2, RDS e Security Groups.
- Criar diagrama e estimativa de custos.
- Commit: `docs: define AWS architecture`.

## Fase 3 — Deploy

- Criar EC2 e RDS.
- Configurar variáveis de ambiente.
- Instalar dependências.
- Executar Gunicorn.
- Validar conexão EC2 → RDS.
- Commit: `feat: deploy API on EC2`.

## Fase 4 — Validação

- Executar testes de saúde e CRUD.
- Confirmar persistência após reinício.
- Revisar regras de segurança.
- Organizar evidências.
- Commit: `test: validate cloud deployment`.

## Fase 5 — Retrospectiva

- Documentar decisões e trade-offs.
- Avaliar 12-Factor App.
- Registrar melhorias futuras.
- Commit: `docs: publish project retrospective`.
