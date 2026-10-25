# 📄 Relatório de Entrega — Tech Challenge Fase 1

**POSTECH · DevOps e Arquitetura Cloud · Projeto ToggleMaster**

## Participantes

| Nome | RM |
|---|---|
| Matheus Menezes | `<RM>` |
| `<nome do participante>` | `<RM>` |

## Links

| Item | Link |
|---|---|
| Vídeo de demonstração | `<link do YouTube>` |
| Documentação | https://github.com/duartecraft/togglemaster-devops-aws |
| Diagrama de arquitetura | https://github.com/duartecraft/togglemaster-devops-aws/blob/main/diagrams/togglemaster-arquitetura-aws.png |
| Diagrama editável (diagrams.net) | https://github.com/duartecraft/togglemaster-devops-aws/blob/main/diagrams/togglemaster-arquitetura-aws.drawio |
| Discussão 12-Factor App | https://github.com/duartecraft/togglemaster-devops-aws/blob/main/docs/12-factor-checklist.md |
| Playbook do deploy na AWS | https://github.com/duartecraft/togglemaster-devops-aws/blob/main/docs/labs/aws-deployment.md |
| Código da aplicação (fork com ajustes) | https://github.com/duartecraft/toggle-master-monolith |

## Resumo da solução

A API monolítica ToggleMaster (Flask + Gunicorn) foi executada localmente com Docker Compose e implantada manualmente na AWS, região us-east-1:

- **Rede:** VPC `10.0.0.0/16` com sub-redes públicas e privadas em duas zonas de disponibilidade.
- **Aplicação:** EC2 `t3.micro` na sub-rede pública, com Gunicorn mantido pelo systemd na porta 5000.
- **Banco:** RDS PostgreSQL `db.t3.micro` na sub-rede privada, sem acesso público e com SSL obrigatório.
- **Segurança:** `sg-app` libera SSH só do IP do administrador e 80/443/5000 ao público; `sg-database` aceita a porta 5432 só a partir do `sg-app`. Usuário IAM com MFA e política de privilégio mínimo.
- **Validação:** API respondendo no IP público, dados persistidos no RDS, e bloqueios comprovados por SSH via 4G, AWS CloudShell e VPC Reachability Analyzer.

## Desafios encontrados e decisões tomadas

| Desafio | Decisão |
|---|---|
| Seguindo o guia da EC2, a tabela `flags` nunca era criada: só o `entrypoint.sh` do Docker fazia isso | A aplicação passou a criar a tabela ao iniciar (`CREATE TABLE IF NOT EXISTS`) |
| `psycopg2-binary 2.9.5` e `gunicorn 20.1.0` falham em Python 3.12+ | Atualizados para 2.9.10 e 23.0.0 |
| No Windows, o `entrypoint.sh` quebrou no Docker por causa da quebra de linha CRLF | Conversão para LF e `.gitattributes` com `*.sh text eol=lf` |
| O RDS exige um DB subnet group em pelo menos duas zonas diferentes | Rede em us-east-1a e us-east-1b; banco Single-AZ (us-east-1b), com a segunda zona livre para um futuro Multi-AZ |
| A AWS não aceita nomes de Security Group começando com `sg-` | Nome técnico `togglemaster-app`/`togglemaster-database`, com Name `sg-app`/`sg-database` |
| Gerenciar a senha do banco sem expô-la | Senha aleatória em `/etc/togglemaster.env` (dono root, `chmod 600`), lida pelo systemd; fora do código, do Git, do user data e do histórico do terminal |
| A aplicação parava ao fechar o SSH | Serviço systemd com reinício automático |
| Custo | Região us-east-1, sem NAT Gateway, Load Balancer ou Multi-AZ; orçamento de US$ 30/mês com alerta por e-mail |

## Estimativa de custos

Calculadora de Preços da AWS, região us-east-1, preço cheio (sem Free Tier), recursos ligados 730 h/mês:

| Item | Uso mensal | US$/mês |
|---|---|---|
| RDS `db.t3.micro` PostgreSQL | 730 h | 13,14 |
| EC2 `t3.micro` Linux | 730 h | 7,59 |
| IPv4 público da EC2 | 730 h | 3,65 |
| RDS armazenamento gp2 | 20 GB | 2,30 |
| EBS gp3 da EC2 | 8 GB | 0,64 |
| Transferência para a internet | 1 GB | 0,09 |
| **Total** | | **27,41** |

![Estimativa na Calculadora de Preços da AWS](../evidence/aws/16-estimativa-de-custos.png)
