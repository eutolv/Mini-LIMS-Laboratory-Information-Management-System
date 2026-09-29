# mini-LIMS

Um pequeno **Laboratory Information Management System** construído em camadas para aprender
Bash, Linux, SQL/SQLite, APIs, testes e arquitetura. Não é pronto para produção (veja "Limitações").

```
Linux → Bash CLI → camada de dados → SQLite (schema + triggers) → regras → validação
      → auditoria → API REST → automação (cron) → testes
```

## Requisitos
Linux, **Bash ≥ 4.4**, **sqlite3 ≥ 3.33**, **Python ≥ 3.8** (só para a API e seus testes; sem pacotes extras),
coreutils (`sha256sum`, `stat`, `flock`). Debian/Ubuntu: `sudo apt install sqlite3 python3 git`.

## Instalação (2 minutos)
```bash
cd mini-lims
bin/lab system init --seed        # cria data/, logs/, backups/, aplica migrations, carrega dados demo
ln -s "$PWD/bin/lab" ~/.local/bin/lab   # opcional: usar só "lab" (garanta ~/.local/bin no PATH)
lab system health
tests/run.sh                      # roda toda a suíte
scripts/demo.sh                   # passeio narrado em banco descartável
```

## Primeiros passos
```bash
lab client list
lab sample create --client ACME --type WATER --collected 2026-09-25 --description "Torneira"
lab sample register SAM-0001
lab request create SAM-0001 PH
lab result add SAM-0001 PH 7.42
lab sample status SAM-0001
lab audit show SAM-0001
```
Ajuda: `lab --help`. Usuários demo: `admin`, `analyst1`, `reviewer1` (troque com `LAB_USER=reviewer1 lab ...`).
Saída JSON: `lab --json sample list`. API: `python3 src/api/server.py` (docs/API.md).

## Estrutura
| Pasta | Papel |
|---|---|
| `bin/lab` | Despachante da CLI |
| `src/lib/` | Bibliotecas Bash: erros/validação, config, acesso ao SQLite, auditoria, permissões |
| `src/cmd/` | Um arquivo por recurso (`sample.sh`, `result.sh`, ...) |
| `src/api/server.py` | API REST (stdlib); chama a CLI, não duplica regras |
| `migrations/` | Schema versionado; **regras de negócio como triggers** |
| `database/seed.sql` | Dados demo |
| `scripts/` | cron, demo |
| `tests/` | Suítes Bash + `test_api.py` |
| `docs/` | Documentação |
| `data/ logs/ backups/` | Estado em runtime (ignorado pelo Git) |

## Git
`.gitignore` exclui banco, logs e backups. Convenção: um commit por marco (`feat:`, `test:`, `docs:`).

## Limitações conhecidas
Sem senhas (identidade via `LAB_USER`/`X-Lab-User`); CSV simples sem campos entre aspas; uma
consulta sqlite por chamada (lento com milhares de linhas); API sem TLS e um processo por requisição;
mudar a especificação de um teste não recalcula flags antigos. Veja docs/ARCHITECTURE.md.
