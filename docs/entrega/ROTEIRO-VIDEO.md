# 🎬 Roteiro do vídeo (até 15 min)

> Os 4 itens obrigatórios do enunciado estão marcados com 📌. Antes de gravar: Docker Desktop aberto, EC2 e RDS ligados, e conferir o IP público da EC2 (ele muda se a instância for parada).

| Tempo | O que mostrar | Item do enunciado |
|---|---|---|
| 0:00–0:45 | Apresentação do grupo e do desafio ToggleMaster | — |
| 0:45–3:00 | `docker compose up --build` e os 5 testes locais (`/health`, `POST`, `GET`, `PUT`, `GET`) | 📌 Aplicação rodando localmente |
| 3:00–5:00 | Diagrama: VPC, sub-redes, IGW, EC2, RDS, Security Groups e o fluxo de uma requisição | 📌 Explicação do diagrama |
| 5:00–6:00 | Problemas do código original e os ajustes no fork (tabela, bibliotecas, CRLF) | Desafios e decisões |
| 6:00–7:30 | VPC → **Resource map**: rota pública para o IGW, privada só local | Rede |
| 7:30–9:30 | EC2 → Security Groups: regras do `sg-app` e do `sg-database` | 📌 Configurações de Security Group |
| 9:30–11:00 | Na EC2: `sudo ls -l /etc/togglemaster.env` (root, 600), `deploy/togglemaster.env.example`, `.gitignore`, usuário IAM com MFA e política mínima | 📌 Como as credenciais foram gerenciadas |
| 11:00–13:00 | `Invoke-RestMethod http://<IP>:5000/flags` do PC e `select * from flags;` no RDS a partir da EC2 | 📌 App na EC2 acessando o RDS |
| 13:00–14:30 | Testes negativos: CloudShell (SSH e banco `BLOQUEADO`) e Reachability Analyzer (2 acessíveis, 2 bloqueados) | Segurança validada |
| 14:30–15:00 | Custo estimado (US$ 27,41/mês) e encerramento | — |

## Cuidados na gravação

- Não mostrar a senha do banco, o conteúdo do `.pem` nem o ID da conta (canto superior direito do console).
- Ao mostrar o `/etc/togglemaster.env`, exiba só a permissão (`ls -l`), não o conteúdo.
- Depois de publicar no YouTube (pode ser "Não listado"), coloque o link no [relatório](RELATORIO-ENTREGA.md).
