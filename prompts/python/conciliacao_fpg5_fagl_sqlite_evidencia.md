# Prompt Codex — Projeto Python: Conciliação SAP FPG5 x FAGL

```text
Você é um agente de desenvolvimento Python.

Crie um projeto Python simples, organizado e de fácil manutenção para a rotina:

CONCILIAÇÃO SAP FPG5 x FAGL

Objetivo do projeto:
Ler arquivos Excel extraídos do SAP, separados entre FPG5 e FAGL, consolidar os dados em SQLite e gerar um arquivo Excel de Evidência da conciliação.

Caminho do projeto no Windows:

C:\Python\Projeto_Conciliacao_FPG5_FAGL

IMPORTANTE:
- Trabalhe somente dentro dessa pasta.
- Se a pasta não existir, crie.
- Se a pasta já existir, não apague nada.
- Antes de sobrescrever qualquer arquivo existente, crie backup com sufixo `_backup_YYYYMMDD_HHMM`.
- Evite comandos PowerShell muito longos.
- Prefira criar scripts pequenos e modulares.
- Se precisar de autorização para escrever fora do diretório atual do sandbox, solicite aprovação normalmente.

---

# 1. Ambiente virtual

Crie um ambiente virtual independente dentro do projeto:

.venv

Comandos esperados:

python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip

Crie os arquivos:

requirements.txt
requirements-dev.txt

No `requirements.txt`, inclua:

polars
fastexcel
openpyxl
xlsxwriter
python-dotenv

No `requirements-dev.txt`, inclua:

pytest
ruff
black
mypy
ipykernel

Observação:
Não inclua no requirements bibliotecas padrão do Python como:

datetime
os
pathlib
sqlite3
sys
time
tkinter
logging

---

# 2. Estrutura de pastas

Crie esta estrutura:

Projeto_Conciliacao_FPG5_FAGL/
│
├── data/
│   └── raw/
│       ├── fpg5/
│       └── fagl/
│
├── database/
├── docs/
├── logs/
├── outputs/
│   └── evidencias/
│
├── scripts/
├── src/
│   ├── config/
│   ├── extract/
│   ├── transform/
│   ├── database/
│   ├── reconciliation/
│   ├── evidence/
│   └── utils/
│
├── tests/
├── agents/
└── skills/

Crie arquivos `.gitkeep` nas pastas vazias importantes.

---

# 3. Arquivos principais

Crie os seguintes arquivos:

README.md
.gitignore
.env.example
pyproject.toml

docs/ESTRUTURA_PASTAS.md
docs/ROTINA_FPG5_FAGL.md
docs/MODELO_SQLITE.md
docs/EVIDENCIA_EXCEL.md

scripts/setup_ambiente.ps1
scripts/run_projeto.ps1

src/main.py
src/config/configuracoes.py
src/utils/logger.py
src/extract/leitor_excel.py
src/extract/validador_cabecalhos.py
src/transform/tratador_valores.py
src/transform/padronizador_colunas.py
src/database/conexao_sqlite.py
src/database/modelo_sqlite.py
src/database/carga_sqlite.py
src/reconciliation/chaves.py
src/reconciliation/comparador_fpg5_fagl.py
src/evidence/gerador_evidencia_excel.py

---

# 4. Banco SQLite

Crie um banco SQLite em:

database/conciliacao_fpg5_fagl.sqlite

Crie funções para montar as tabelas abaixo:

1. controle_arquivos

Campos:
- id_arquivo
- origem_base
- nome_arquivo
- caminho_arquivo
- data_processamento
- qtd_linhas_lidas
- qtd_linhas_processadas
- status_processamento
- mensagem_erro

2. fpg5_lancamentos

Campos mínimos:
- id_linha
- arquivo_origem
- empresa
- conta_razao
- data_lancamento
- documento
- item_documento
- chave_reconciliacao
- texto
- montante
- montante_ajustado
- chave_conciliacao
- data_carga

3. fagl_lancamentos

Campos mínimos:
- id_linha
- arquivo_origem
- empresa
- conta_razao
- data_lancamento
- documento
- item_documento
- chave_reconciliacao
- texto
- montante
- montante_ajustado
- chave_conciliacao
- data_carga

4. conciliacao_resultado

Campos:
- id_resultado
- chave_conciliacao
- empresa
- conta_razao
- data_lancamento
- documento
- valor_fpg5
- valor_fagl
- diferenca
- status_conciliacao
- observacao
- data_processamento

Status possíveis:
- CONCILIADO
- DIVERGENTE_VALOR
- SOMENTE_FPG5
- SOMENTE_FAGL
- CHAVE_NULA
- ERRO

---

# 5. Regras iniciais de conciliação

Crie a estrutura para comparar FPG5 x FAGL por chave configurável.

A chave inicial pode usar:

empresa
conta_razao
data_lancamento
documento
item_documento

A tolerância monetária inicial deve ser:

0.01

A lógica deve permitir:

- agregar valores por chave;
- comparar FPG5 contra FAGL;
- identificar registros conciliados;
- identificar somente FPG5;
- identificar somente FAGL;
- identificar divergência de valor acima da tolerância.

---

# 6. Excel de Evidência

Gere um arquivo Excel em:

outputs/evidencias/

Nome sugerido:

YYYY-MM-DD_HHMM_evidencia_conciliacao_fpg5_fagl.xlsx

O arquivo deve conter as abas:

1. Capa
2. Parametros
3. Arquivos_Processados
4. Validacao_Cabecalhos
5. Resumo_Conciliacao
6. Resultado_Detalhado
7. Somente_FPG5
8. Somente_FAGL
9. Divergencias
10. Logs

Use `xlsxwriter` para criar o Excel.

Aplique:
- filtros;
- tabelas;
- congelamento de cabeçalho;
- largura ajustada de colunas;
- formato numérico para valores;
- destaque visual por status.

---

# 7. Código Python

Implemente código inicial simples, mesmo que ainda seja uma estrutura básica.

O `src/main.py` deve executar o fluxo:

1. Criar pastas necessárias.
2. Inicializar logger.
3. Criar ou validar banco SQLite.
4. Listar arquivos Excel da pasta `data/raw/fpg5`.
5. Listar arquivos Excel da pasta `data/raw/fagl`.
6. Validar cabeçalhos.
7. Carregar dados no SQLite.
8. Criar chaves de conciliação.
9. Comparar FPG5 x FAGL.
10. Gerar Excel de Evidência.
11. Exibir o caminho do arquivo gerado.

Não precisa implementar uma regra perfeita para todos os layouts SAP agora.
O mais importante é deixar a arquitetura preparada e fácil de adaptar.

Use:
- pathlib para caminhos;
- logging para logs;
- sqlite3 para SQLite;
- polars para leitura e transformação de dados;
- openpyxl para validação de cabeçalhos;
- xlsxwriter para o Excel de Evidência.

---

# 8. Documentação

Crie documentação simples em português.

O README.md deve explicar:

- objetivo do projeto;
- estrutura das pastas;
- onde colocar arquivos FPG5;
- onde colocar arquivos FAGL;
- como ativar o ambiente virtual;
- como instalar dependências;
- como executar o projeto;
- onde encontrar o SQLite;
- onde encontrar o Excel de Evidência.

O arquivo `docs/ESTRUTURA_PASTAS.md` deve explicar para que serve cada pasta.

O arquivo `docs/ROTINA_FPG5_FAGL.md` deve explicar o fluxo da conciliação.

O arquivo `docs/MODELO_SQLITE.md` deve explicar as tabelas criadas.

O arquivo `docs/EVIDENCIA_EXCEL.md` deve explicar as abas do arquivo de evidência.

---

# 9. Agents e Skills

Crie apenas modelos simples em Markdown.

Na pasta `agents/`, crie:

agente_arquitetura_python.md
agente_etl_sap_excel.md
agente_sqlite.md
agente_conciliacao_fpg5_fagl.md
agente_evidencia_excel.md

Cada agente deve conter:
- papel;
- quando usar;
- entradas esperadas;
- saídas esperadas;
- checklist.

Na pasta `skills/`, crie:

skill_ler_excel_sap/SKILL.md
skill_validar_cabecalhos/SKILL.md
skill_consolidar_sqlite/SKILL.md
skill_criar_chaves_conciliacao/SKILL.md
skill_comparar_fpg5_fagl/SKILL.md
skill_exportar_evidencia_excel/SKILL.md

Cada Skill deve conter:
- objetivo;
- quando usar;
- entrada;
- saída;
- exemplo simples.

Não crie textos excessivamente longos.

---

# 10. Scripts PowerShell

Crie:

scripts/setup_ambiente.ps1

Esse script deve:
- criar `.venv` se não existir;
- ativar `.venv`;
- atualizar pip;
- instalar requirements;
- instalar requirements-dev.

Crie:

scripts/run_projeto.ps1

Esse script deve:
- ativar `.venv`;
- executar `src/main.py`.

---

# 11. Testes iniciais

Crie testes simples:

tests/test_tratador_valores.py
tests/test_chaves.py

Os testes devem validar:

- conversão de número brasileiro para decimal;
- criação de chave de conciliação;
- comparação simples entre valor FPG5 e FAGL.

---

# 12. Validação final

Ao final, informe:

- caminho do projeto;
- se o `.venv` foi criado;
- arquivos criados;
- pastas criadas;
- bibliotecas registradas;
- caminho do SQLite;
- caminho esperado da evidência;
- comando para ativar ambiente;
- comando para executar o projeto;
- próximos passos.

Priorize simplicidade, clareza e manutenção fácil.
```
