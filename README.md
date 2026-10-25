# 🚀 ToggleMaster on AWS

> Feature Flag as a Service — **POSTECH DevOps e Arquitetura Cloud · Tech Challenge Fase 1**

API monolítica em Python (Flask + Gunicorn) executada localmente com Docker Compose e implantada manualmente na AWS: **EC2 em sub-rede pública** e **RDS PostgreSQL em sub-rede privada**, com Security Groups de privilégio mínimo.

![Status](https://img.shields.io/badge/deploy-AWS%20us--east--1-orange) ![Stack](https://img.shields.io/badge/stack-Flask%20%7C%20Gunicorn%20%7C%20PostgreSQL-blue) ![Custo](https://img.shields.io/badge/custo%20estimado-US%24%2027%2C41%2Fm%C3%AAs-green)

## 📦 Entregáveis da Fase 1

| Entregável (enunciado) | Onde está | Status |
|---|---|---|
| Vídeo de demonstração (até 15 min) | Link no relatório de entrega · [roteiro](docs/entrega/ROTEIRO-VIDEO.md) | ⏳ Gravação |
| Link para o diagrama de arquitetura | [`diagrams/togglemaster-arquitetura-aws.png`](diagrams/togglemaster-arquitetura-aws.png) · [`.drawio`](diagrams/togglemaster-arquitetura-aws.drawio) | ✅ |
| Discussão sobre o 12-Factor App aplicada ao projeto | [`docs/12-factor-checklist.md`](docs/12-factor-checklist.md) | ✅ |
| Relatório de entrega (.pdf ou .txt) | [`docs/entrega/RELATORIO-ENTREGA.md`](docs/entrega/RELATORIO-ENTREGA.md) · PDF na mesma pasta | ✅ (falta o link do vídeo) |
| Estimativa de custos (print) | [Relatório](docs/entrega/RELATORIO-ENTREGA.md#estimativa-de-custos) · [evidência](docs/evidence/aws/16-estimativa-de-custos.png) | ✅ |

## 🧱 Arquitetura

![Arquitetura AWS](diagrams/togglemaster-arquitetura-aws.png)

```text
Usuário ──:5000──▶ Internet Gateway ──▶ EC2 (pública, us-east-1a) ──:5432 SSL──▶ RDS PostgreSQL (privada, us-east-1b)
```

| Requisito do enunciado | Implementação |
|---|---|
| VPC com sub-redes públicas e privadas | VPC `10.0.0.0/16` · públicas `10.0.1.0/24` e `10.0.2.0/24` · privadas `10.0.11.0/24` e `10.0.12.0/24` |
| EC2 na sub-rede pública | `t3.micro`, Amazon Linux 2023, Gunicorn via systemd |
| RDS (PostgreSQL ou MySQL) na sub-rede privada | PostgreSQL 18.3, `db.t3.micro`, sem acesso público |
| SG da EC2 com HTTP/HTTPS e SSH de IP específico | `sg-app`: 22 ← IP do administrador /32 · 80, 443 e 5000 ← `0.0.0.0/0` |
| SG do RDS só a partir do SG da EC2 | `sg-database`: 5432 ← `sg-app` |

Detalhes e decisões: [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md).

## ✅ Evidências

| | |
|---|---|
| **API no IP público da EC2** ![](docs/evidence/aws/13-api-publica-do-pc.png) | **Dados gravados no RDS** ![](docs/evidence/aws/11-ec2-servico-e-banco.png) |
| **Security Groups** ![](docs/evidence/aws/09-security-groups.png) | **Testes de rede (Reachability Analyzer)** ![](docs/evidence/aws/15-reachability-analyzer.png) |

Todas as evidências, com dados sensíveis borrados: [`docs/evidence/aws/`](docs/evidence/aws/).

## 📚 Documentação

| Documento | Conteúdo |
|---|---|
| [`docs/labs/local-execution.md`](docs/labs/local-execution.md) | Executar e testar localmente com Docker (Linux e Windows) |
| [`docs/labs/aws-deployment.md`](docs/labs/aws-deployment.md) | **Playbook AWS**: passo a passo com evidências, de IAM a validação e custos |
| [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Diagrama, componentes, regras e decisões |
| [`docs/12-factor-checklist.md`](docs/12-factor-checklist.md) | Análise dos 12 Fatores e do monolito |
| [`docs/entrega/`](docs/entrega/) | Relatório de entrega e roteiro do vídeo |

## 🔗 Código

- Aplicação com os ajustes para a nuvem: [`duartecraft/toggle-master-monolith`](https://github.com/duartecraft/toggle-master-monolith) (fork de [`dougls/toggle-master-monolith`](https://github.com/dougls/toggle-master-monolith))
- Ajustes: criação da tabela na inicialização, bibliotecas compatíveis com Python 3.12+, SSL e timeout no banco, serviço systemd e modelo de `.env`.

## ⚠️ Segurança

Nenhuma senha, chave SSH, arquivo `.env`, ID de conta ou IP administrativo é versionado. Consulte [`SECURITY.md`](SECURITY.md).

## 👤 Autor

Matheus Menezes — Solutions Engineer | Cloud | DevOps | Infrastructure
