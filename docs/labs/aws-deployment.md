# ☁️ Laboratório AWS

## Fluxo

1. Criar VPC.
2. Criar subnet pública e privada.
3. Criar Internet Gateway e rotas.
4. Criar Security Groups.
5. Criar RDS PostgreSQL privado.
6. Criar EC2 pública.
7. Conectar por SSH.
8. Instalar Git e Python.
9. Configurar `DB_HOST`, `DB_NAME`, `DB_USER` e `DB_PASSWORD`.
10. Executar Gunicorn.
11. Validar `/health` e CRUD.
12. Confirmar persistência após reinício.

## Segurança

SSH deve ser limitado ao IP administrativo. O RDS deve aceitar PostgreSQL somente do Security Group da EC2. Nunca versionar credenciais.

## Evidências

Salvar apenas prints anonimizados em `docs/evidence/`.
