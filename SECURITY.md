# 🔐 Segurança

## Nunca versionar

- Chaves `.pem`.
- Senhas do RDS.
- Arquivos `.env`.
- Access Keys da AWS.
- Tokens e credenciais.
- Dados de clientes.
- Endpoints sensíveis acompanhados de credenciais.

## Controles do laboratório

- Restringir SSH ao IP pessoal.
- Não expor o RDS publicamente.
- Permitir PostgreSQL somente do Security Group da EC2.
- Usar dados fictícios ou anonimizados.
- Criar alertas de custo.
- Excluir recursos após o laboratório, quando aplicável.
