# 🏗️ Arquitetura AWS

## Arquitetura da Fase 1

```text
Internet
   |
   v
Internet Gateway
   |
   v
VPC
├── Subnet pública
│   └── EC2 + ToggleMaster + Gunicorn
└── Subnet privada
    └── RDS PostgreSQL
```

## Regras principais

| Origem | Destino | Porta | Motivo |
|---|---|---:|---|
| Seu IP (/32) | EC2 | 22 | SSH administrativo |
| Internet | EC2 | 5000 | API durante o laboratório |
| Internet | EC2 | 80 / 443 | HTTP/HTTPS exigidos pelo enunciado |
| Security Group da EC2 | RDS | 5432 | PostgreSQL |
| Internet | RDS | 5432 | Bloqueado |

## Decisões

- EC2 pública para simplificar a demonstração do MVP.
- RDS privado para reduzir exposição do banco.
- Security Group do RDS referenciando o Security Group da EC2.
- Credenciais fornecidas por variáveis de ambiente.

## Implementação real (us-east-1)

| Componente | Valor |
|---|---|
| VPC | `10.0.0.0/16` |
| Sub-rede pública (EC2) | `10.0.1.0/24` · us-east-1a |
| Sub-rede privada (RDS) | `10.0.12.0/24` · us-east-1b |
| Sub-rede privada (2ª zona do DB subnet group) | `10.0.11.0/24` · us-east-1a |
| EC2 | `t3.micro` · Amazon Linux 2023 · Gunicorn via systemd |
| RDS | PostgreSQL 18.3 · `db.t3.micro` · sem acesso público |
| Custo estimado | US$ 27,41/mês |

Diagrama: [`diagrams/togglemaster-arquitetura-aws.png`](../diagrams/togglemaster-arquitetura-aws.png) · passo a passo com evidências: [`docs/labs/aws-deployment.md`](labs/aws-deployment.md).
