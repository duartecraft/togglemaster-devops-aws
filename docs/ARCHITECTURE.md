# 🏗️ Arquitetura AWS

> Implementada e auditada na conta AWS (região **us-east-1**). Passo a passo com evidências: [`docs/labs/aws-deployment.md`](labs/aws-deployment.md).

![Diagrama da arquitetura](../diagrams/togglemaster-arquitetura-aws.png)

Arquivo editável: [`diagrams/togglemaster-arquitetura-aws.drawio`](../diagrams/togglemaster-arquitetura-aws.drawio) (abrir no [diagrams.net](https://app.diagrams.net) → *Arquivo → Abrir de → Dispositivo*).

## Fluxo de uma requisição

```text
Usuário ──HTTP :5000──▶ Internet Gateway ──▶ Sub-rede pública (us-east-1a)
                                              └─ EC2 · Gunicorn + Flask  [sg-app]
                                                     │ PostgreSQL :5432 (SSL, rede interna da VPC)
                                                     ▼
                                              Sub-rede privada (us-east-1b)
                                              └─ RDS PostgreSQL           [sg-database]
```

## Componentes

| Componente | Valor real | Observação |
|---|---|---|
| VPC | `10.0.0.0/16` | DNS hostnames ligado |
| Internet Gateway | `igw-05239f9257292a45f` | Entrada e saída da internet |
| Sub-rede pública 1 | `10.0.1.0/24` · us-east-1a | **EC2**; rota `0.0.0.0/0 → IGW` |
| Sub-rede pública 2 | `10.0.2.0/24` · us-east-1b | Vazia; simetria entre zonas |
| Sub-rede privada 1 | `10.0.11.0/24` · us-east-1a | Vazia; 2ª zona do DB subnet group |
| Sub-rede privada 2 | `10.0.12.0/24` · us-east-1b | **RDS**; só rota `local` |
| EC2 | `t3.micro` · Amazon Linux 2023 | Gunicorn via systemd na porta 5000 |
| RDS | PostgreSQL 18.3 · `db.t3.micro` · Single-AZ | Sem acesso público; SSL obrigatório |

## Regras de acesso (Security Groups)

| Origem | Destino | Porta | Motivo |
|---|---|---:|---|
| IP do administrador (/32) | EC2 (`sg-app`) | 22 | SSH administrativo |
| Internet (`0.0.0.0/0`) | EC2 (`sg-app`) | 5000 | API do ToggleMaster |
| Internet (`0.0.0.0/0`) | EC2 (`sg-app`) | 80 e 443 | HTTP/HTTPS exigidos pelo enunciado |
| `sg-app` | RDS (`sg-database`) | 5432 | PostgreSQL, só a partir da EC2 |
| Internet | RDS | 5432 | **Bloqueado** (sem IP público e sem regra) |

## Decisões de arquitetura

| Decisão | Por quê |
|---|---|
| Região **us-east-1** (N. Virginia) | Custa cerca de metade de São Paulo (o RDS sai ~US$ 13/mês contra ~US$ 28/mês). Para um MVP de laboratório, a latência maior não pesa. |
| Rede em **duas zonas de disponibilidade diferentes**: us-east-1a e us-east-1b | **Exigência do RDS.** Para criar um banco, a AWS pede um *DB subnet group* com sub-redes em pelo menos duas zonas **diferentes**. Por isso há uma sub-rede privada em cada zona. A sub-rede pública foi duplicada pelo mesmo motivo de simetria, mas a EC2 usa só a da us-east-1a. |
| Banco **Single-AZ** | Não contradiz a decisão anterior. O subnet group só define **em quais zonas** o banco pode rodar; o Single-AZ define que **uma única instância** roda, numa zona só. A AWS escolheu a us-east-1b. A sub-rede da us-east-1a fica vazia e pronta para um futuro Multi-AZ, que dobraria o custo do banco e não é exigido nesta fase. |
| EC2 em sub-rede pública | Simplifica a demonstração do MVP: a API responde direto no IP público. |
| RDS em sub-rede privada | O banco não tem IP público nem rota para a internet; só a EC2 o alcança. |
| Regra do RDS referenciando o **Security Group** da EC2, e não um IP | Continua válida se a EC2 trocar de IP, e recusa qualquer outra máquina, mesmo dentro da VPC. |
| Sem NAT Gateway | Custaria ~US$ 32/mês, e o banco não precisa acessar a internet. |
| Sem Load Balancer | A API é acessada pelo IP da EC2. HTTPS com ALB e certificado do ACM fica para as próximas fases. |
| Credenciais em `/etc/togglemaster.env` (`chmod 600`, dono root), lidas pelo systemd | Senha fora do código, do Git, do user data e do histórico do terminal (fator III dos 12 Fatores). |

> EC2 (us-east-1a) e RDS (us-east-1b) ficaram em zonas diferentes. Isso não afeta o funcionamento; o tráfego entre zonas custa ~US$ 0,01/GB, centavos neste projeto.
