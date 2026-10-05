# Documentação do Pipeline de Deploy

Este workflow (`deploy.yml`) é responsável por realizar o deploy automático de três serviços (banco de dados, API e frontend) em um servidor remoto sempre que houver um `push` na branch `main`.

## ⚙️ Variáveis de Ambiente e Secrets

O pipeline depende das seguintes variáveis e secrets:
- `env.SLUG`: Identificador do aluno (ex: `mariano-santos`).
- `env.PORTA`: A porta externa em que o frontend será servido (ex: `23200`).
- `secrets.DEPLOY_KEY`: Chave SSH privada necessária para autenticar no servidor `gr.lab.ayty.org`.
- `secrets.DB_SENHA`: Senha configurada para o banco de dados PostgreSQL.

## 🚀 Como funciona

O processo do deploy ocorre de forma idempotente, ou seja, pode ser rodado múltiplas vezes de forma segura:
1. **Derrubar antiga topologia**: Os containers anteriores são finalizados e removidos.
2. **Redes e Volumes**: São criadas as redes lógicas (`web` e `dados`) e o volume do Postgres.
3. **Subir Banco de Dados**: Um container do PostgreSQL (`16-alpine`) é iniciado na rede de dados.
4. **Subir API**: O container da API (`gr-atv2-api:1`) sobe e se conecta ao banco.
5. **Subir Front**: O frontend é iniciado e sua porta é vinculada com o acesso externo.
6. **Health Check**: Ao final, o pipeline testa o endpoint do frontend por até 60 segundos para validar se a aplicação realmente subiu.

## 🛠 Depuração e Erros

Se o pipeline falhar:
- Verifique os logs no GitHub Actions. Cada etapa roda com `set -x`, o que exibe os comandos exatos que falharam.
- Garanta que a porta configurada em `PORTA` não está em uso no servidor remoto.
- Verifique se a variável `secrets.DEPLOY_KEY` é válida e está carregada corretamente nas configurações do repositório.
