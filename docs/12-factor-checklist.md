# 🧩 12-Factor App aplicado ao ToggleMaster

> Entregável: *"Discussão sobre os 12-Factor App aplicada ao projeto."*
> Requisito: *"Leia e compreenda os 12 Fatores e identifique quais deles a aplicação já atende e quais precisariam de ajustes para um ambiente de produção mais robusto."*
> Referências: [12factor.net](https://12factor.net/pt_br/) e o material da disciplina [`josenetoo/fiap-demo-12-factor-app`](https://github.com/josenetoo/fiap-demo-12-factor-app/blob/main/docs/12-factor-app.md).

## Resumo

Avaliamos a versão que está rodando na AWS ([fork com os ajustes](https://github.com/duartecraft/toggle-master-monolith)). Regra usada: nenhum fator foi marcado como atendido sem evidência no código, na configuração ou na execução.

| Situação | Fatores |
|---|---|
| ✅ Atende (5) | I Codebase · III Config · IV Backing services · VI Processes · XI Logs |
| 🟡 Atende em parte (6) | II Dependencies · VII Port binding · VIII Concurrency · IX Disposability · X Dev/prod parity · XII Admin processes |
| ❌ Não atende (1) | V Build, release, run |

Para um MVP monolítico, o resultado é coerente: a aplicação já separa configuração do código e trata o banco como recurso externo, que são os fatores que mais travam uma ida para a nuvem. O que falta está concentrado em **automação de entrega** (V e X), que é o tema das próximas fases.

## Análise fator a fator

| Fator | O que pede | Evidência no ToggleMaster | Situação | Ajuste para produção |
|---|---|---|---|---|
| **I. Codebase** | Um repositório versionado, vários deploys | Um repositório Git; o mesmo código roda no Docker local e na EC2 | ✅ | — |
| **II. Dependencies** | Dependências declaradas e isoladas | `requirements.txt` com versões fixas; `venv` na EC2; imagem Docker | 🟡 | Declarar a versão do Python e travar as dependências indiretas (ex.: `pip-tools`) |
| **III. Config** | Configuração no ambiente, fora do código | `app.py` lê `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_SSLMODE` com `os.getenv`; na EC2, o systemd injeta `/etc/togglemaster.env` (`chmod 600`) | ✅ | Guardar a senha no AWS Secrets Manager ou no SSM Parameter Store |
| **IV. Backing services** | Serviços de apoio como recursos anexados | O PostgreSQL do Docker virou RDS trocando só variáveis, sem mudar código | ✅ | — |
| **V. Build, release, run** | Fases separadas, release rastreável | Na EC2, o deploy é `git clone` + `pip install` direto no servidor; não há artefato versionado nem rollback | ❌ | Pipeline de CI gerando imagem com a tag do commit; deploy por release |
| **VI. Processes** | Processos stateless | Nada fica em memória entre requisições; o estado vive no PostgreSQL | ✅ | — |
| **VII. Port binding** | A app expõe a própria porta | O Gunicorn expõe a API sozinho, sem servidor web externo | 🟡 | Ler a porta de uma variável `PORT`, em vez de fixar 5000 no comando |
| **VIII. Concurrency** | Escalar por processos | Gunicorn com 2 workers numa única EC2 | 🟡 | Várias EC2 atrás de um Load Balancer (Auto Scaling) e pool de conexões com o banco |
| **IX. Disposability** | Início rápido e parada graciosa | O Gunicorn trata `SIGTERM`; o systemd reinicia em 5 s; a app sobe em ~1 s | 🟡 | Timeout de encerramento explícito e `/health` que verifique também o banco |
| **X. Dev/prod parity** | Ambientes semelhantes | Local usa Docker; a EC2 roda direto no sistema. Foi essa diferença que escondeu o bug da tabela `flags` | 🟡 | Rodar a mesma imagem Docker em todos os ambientes |
| **XI. Logs** | Logs como fluxo em stdout | Tudo vai para stdout; na EC2, lido com `journalctl -u togglemaster` | ✅ | Enviar para o CloudWatch Logs |
| **XII. Admin processes** | Tarefas administrativas em processos pontuais | Existe o comando `flask init-db`, mas a criação da tabela também roda na subida da app | 🟡 | Migrations como tarefa separada (ex.: Alembic) executada antes do deploy |

## O caso que melhor ilustra o fator X

Localmente, tudo funcionava: o `entrypoint.sh` do Docker rodava `flask init-db` e criava a tabela `flags`. Seguindo o guia da EC2, porém, a app sobe direto pelo Gunicorn, sem passar pelo `entrypoint.sh`. Resultado: `/health` respondia `ok`, mas o primeiro `POST /flags` falhava com `relation "flags" does not exist`.

A correção foi a app criar a tabela ao iniciar. A lição é a do fator X: **"funciona na minha máquina" escondia um passo que só existia dentro do contêiner.** Em produção, a resposta definitiva é usar o mesmo artefato (a imagem Docker) em todos os ambientes, como faz a demo da disciplina.

## Monolito: vantagens e desvantagens para o MVP

Rotas HTTP, regras de negócio e acesso ao banco estão num único arquivo, `app.py`, implantado como um único processo.

| | Vantagem para o MVP | Desvantagem |
|---|---|---|
| Desenvolvimento | Simples de entender; um só repositório | Tudo acoplado; uma mudança mexe no todo |
| Deploy | Um único artefato | Qualquer correção exige redeploy completo |
| Escala | Basta replicar o processo | Escala tudo junto, mesmo a parte ociosa |
| Falhas | Fácil de depurar localmente | Um erro pode derrubar a aplicação inteira |

Para validar a ideia do ToggleMaster rapidamente, o monolito é a escolha certa. As fases seguintes do curso tratam da evolução para uma arquitetura distribuída.
