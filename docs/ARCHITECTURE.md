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
| Seu IP | EC2 | 22 | SSH administrativo |
| Internet | EC2 | 5000 | API durante o laboratório |
| Security Group da EC2 | RDS | 5432 | PostgreSQL |
| Internet | RDS | 5432 | Bloqueado |

## Decisões

- EC2 pública para simplificar a demonstração do MVP.
- RDS privado para reduzir exposição do banco.
- Security Group do RDS referenciando o Security Group da EC2.
- Credenciais fornecidas por variáveis de ambiente.

> Validar região, preços, versões e disponibilidade antes da execução.
