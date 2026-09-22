# 🚀 ToggleMaster on AWS

> Feature Flag as a Service — DevOps Tech Challenge | Fase 1

Projeto prático de Cloud, DevOps e Solutions Engineering: execução local, arquitetura AWS e deploy manual de uma API monolítica Python conectada ao PostgreSQL.

## 🎯 Objetivos

- Entender uma aplicação monolítica.
- Executar a API localmente com Docker Compose.
- Projetar uma arquitetura com VPC, EC2 e RDS.
- Aplicar isolamento de rede e Security Groups.
- Validar persistência e operação da API.


## 🧱 Arquitetura-alvo

```text
Cliente → Internet Gateway → EC2 pública → ToggleMaster API → RDS PostgreSQL privado
```

## 🗺️ Jornada do projeto

| Fase | Foco | Commit | Conteúdo |
|---|---|---|---|
| 01 | Execução local com Docker | `feat: run ToggleMaster locally` | Post sobre containers e API |
| 02 | Arquitetura AWS | `docs: define AWS architecture` | Post sobre VPC e segurança |
| 03 | Deploy EC2 + RDS | `feat: deploy API on EC2` | Post sobre deploy e banco |
| 04 | Validação e segurança | `test: validate cloud deployment` | Post sobre evidências |
| 05 | Retrospectiva | `docs: publish project retrospective` | Post final sobre trade-offs |

## 📁 Estrutura

- `docs/ROADMAP.md`: plano de execução.
- `docs/ARCHITECTURE.md`: desenho e decisões arquiteturais.
- `docs/12-factor-checklist.md`: análise do 12-Factor App.
- `docs/labs/`: procedimentos reproduzíveis.
- `docs/linkedin/`: rascunhos dos posts.
- `docs/evidence/`: evidências sem dados sensíveis.
- `diagrams/`: diagramas exportados.

## ⚠️ Segurança

Nunca versionar senhas, chaves SSH, arquivos `.env`, tokens ou dados de clientes. Consulte `SECURITY.md`.

## 📚 Projeto-base

Aplicação fornecida para o Tech Challenge: `dougls/toggle-master-monolith`.

## 👤 Autor

Matheus Menezes — Solutions Engineer | Cloud | DevOps | Infrastructure
