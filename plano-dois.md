# Scout — Buscador de Vagas

## Visão Geral

Agente especializado em busca de oportunidades de emprego que utiliza a ferramenta Firecrawl para pesquisar vagas em múltiplas plataformas (Indeed, Catho, LinkedIn, Glassdoor, Infojobs) e retornar resultados estruturados para o usuário.

**Objetivo**: Criar o Scout — agente que busca vagas de emprego com base no perfil do usuário, utilizando Firecrawl CLI para extração de dados web.

## Contexto do Firecrawl

O Firecrawl está instalado e disponível via CLI através do comando `npx`. Esta seção deve ser incluída em todos os arquivos relacionados ao Scout.

### Instalação e Disponibilidade

```bash
npx -y firecrawl-cli@latest init --all --browser
```

### Comandos Principais para o Scout

1. **Busca web** (para encontrar vagas):
   ```bash
   firecrawl search "desenvolvedor frontend junior remoto" --limit 10
   ```

2. **Extração de página** (quando URL da vaga é conhecida):
   ```bash
   firecrawl scrape "https://www.indeed.com/vaga/12345" -o .firecrawl/vaga.md
   ```

3. **Interação com páginas** (se necessário para paginação ou filtros):
   ```bash
   firecrawl interact "https://www.linkedin.com/jobs" --click "Ver mais vagas"
   ```

4. **Mapeamento de site** (para descobrir URLs de vagas):
   ```bash
   firecrawl map "https://www.catho.com.br" --search "desenvolvedor"
   ```

### Fallback

Caso o Firecrawl falhe ou não esteja disponível, o Scout deve utilizar a ferramenta nativa de acesso web (WebSearch/WebFetch) para realizar as buscas.

### Verificação de Instalação

Antes de usar, verificar se o Firecrawl está funcional:

```bash
firecrawl --status
firecrawl scrape "https://firecrawl.dev" -o .firecrawl/check.md
```

## Arquitetura

```
┌─────────────────────────────────────────────────┐
│                  Usuário                        │
└────────────────────┬────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│              MAESTRO (Orquestrador)             │
│  - Recebe solicitação de busca de vagas        │
│  - Despacha Scout com perfil do usuário         │
│  - Recebe e apresenta resultados               │
└────────────────────┬────────────────────────────┘
                     │ Despacha
                     ▼
┌─────────────────────────────────────────────────┐
│              SCOUT (Buscador de Vagas)          │
│  - Recebe perfil do usuário                     │
│  - Constrói queries de busca                    │
│  - Usa Firecrawl para buscar vagas              │
│  - Extrai dados estruturados das vagas          │
│  - Retorna lista de vagas formatada             │
└─────────────────────────────────────────────────┘
```

## Estrutura de Diretórios

```
recoloca-ia/
├── AGENTS.md                   # Instruções de inicialização
├── plano.md                    # Plano do Maestro
├── plano-dois.md               # Plano do Scout (este arquivo)
├── personas/
│   ├── maestro.md              # Orquestrador principal
│   └── scout.md                # Agente buscador de vagas (NOVO)
├── skills/
│   ├── dispatch.md             # Protocolo de despacho
│   ├── firecrawl.md            # Documentação Firecrawl
│   └── job-search.md           # Skill de busca de vagas (NOVO)
└── data/
    ├── personality-quiz.md     # Quiz de personalidade
    ├── user-profile.md         # Perfil do usuário
    └── job-listings.md         # Vagas encontradas (NOVO)
```

## Tasks

### 1. Criar `personas/scout.md`

O Scout deve conter:

**Responsabilidade**: Buscar vagas de emprego com base no perfil do usuário, utilizando Firecrawl para extração de dados web.

**Contexto do Firecrawl** (sempre incluído no início da persona):
```
## Contexto Firecrawl
O Firecrawl está disponível via CLI (`npx firecrawl-cli`).
Comandos principais:
- Busca: `firecrawl search "query" --limit N`
- Extração: `firecrawl scrape "URL" -o output.md`
- Interação: `firecrawl interact "URL" --click "botão"`
- Mapeamento: `firecrawl map "URL" --search "termo"`
Se o Firecrawl falhar, usar ferramenta nativa de acesso web.
```

**Skills**:
- `skills/dispatch.md` — Protocolo de despacho (DEVE carregar)
- `skills/job-search.md` — Lógica de busca de vagas (DEVE carregar)
- `skills/firecrawl.md` — Referência de comandos Firecrawl

**Ferramentas**:
- `firecrawl search` — busca web por vagas
- `firecrawl scrape` — extrai conteúdo de páginas de vagas
- `firecrawl interact` — interage com páginas (filtros, paginação)
- Ferramenta nativa web (fallback se Firecrawl falhar)

**Arquivos de Entrada**:
- `data/user-profile.md` — perfil do usuário com área, nível, localização, habilidades

**Arquivos de Saída**:
- `data/job-listings.md` — lista de vagas encontradas

**Fluxo de Execução**:
1. Receber perfil do usuário via envelope de despacho
2. Construir queries de busca baseadas no perfil:
   - Função alvo + Nível + Localização
   - Exemplo: "Desenvolvedor Frontend Júnior Remoto"
3. Executar buscas no Firecrawl para cada plataforma:
   - Indeed
   - Catho
   - LinkedIn Jobs
   - Glassdoor
   - Infojobs
4. Para cada resultado relevante, extrair dados estruturados:
   - Título da vaga
   - Empresa
   - Localização
   - Nível
   - Descrição resumida
   - URL da vaga
   - Data de publicação (se disponível)
5. Filtrar vagas irrelevantes ou duplicadas
6. Salvar resultados em `data/job-listings.md`
7. Retornar envelope de resposta com vagas encontradas

**Formato de Saída** (para `data/job-listings.md`):

```
## Vagas Encontradas

### Vaga 1
Título: [valor]
Empresa: [valor]
Localização: [valor]
Nível: [valor]
Descrição: [resumo de 2-3 frases]
URL: [valor]
Data: [valor]
Fonte: [Indeed|Catho|LinkedIn|Glassdoor|Infojobs]

### Vaga 2
...
```

**Tratamento de Erros**:
- Se Firecrawl falhar: tentar ferramenta nativa web
- Se busca não retornar resultados: ampliar critérios (ex: remover filtro de localização)
- Se extração falhar: incluir URL na lista e marcar como "ver detalhes no link"
- Sempre reportar erros no campo `erros` do envelope de resposta

### 2. Criar `skills/job-search.md`

Skill contendo:
- Estratégias de busca por plataforma
- Mapeamento de termos de busca baseado no perfil
- Filtros e critérios de relevância
- Formatação de queries para o Firecrawl
- Parsing de resultados de vagas
- Dicas para evitar bloqueios e rate limits

**Estrutura da Skill**:

```
# Job Search Skill

## Plataformas Alvo
1. Indeed - busca ampla, bom para filtros de localização
2. Catho - foco em Brasil, vagas nacionais
3. LinkedIn Jobs - networking, vagas corporativas
4. Glassdoor - salários e avaliações de empresas
5. Infojobs - mercado brasileiro

## Construção de Queries

Baseado no perfil do usuário:
- Função alvo (de user-profile.md)
- Nível de experiência
- Localização (ou "Remoto")
- Habilidades chave (opcional)

Exemplo de query:
"Desenvolvedor Frontend Júnior Remoto"
"Python Developer Pleno São Paulo"
"Data Scientist Senior Remote"

## Uso do Firecrawl

### Busca Inicial
firecrawl search "query site:indeed.com.br OR site:catho.com.br" --limit 20

### Extração de Detalhes
Para cada URL de vaga encontrada:
firecrawl scrape "URL_DA_VAGA" -o .firecrawl/vaga-[N].md

### Parsing de Resultados
Extrair campos:
- Título (h1 ou tag de título da vaga)
- Empresa (geralmente próximo ao título)
- Localização (cidade/estado ou "Remoto")
- Descrição (primeiros parágrafos ou resumo)
- URL (URL original)

## Critérios de Relevância
- Vagas que contenham a função alvo no título
- Nível compatível (ou inferior, nunca superior)
- Localização compatível (ou remoto)
- Publicadas nos últimos 30 dias (se data disponível)

## Fallback
Se Firecrawl não funcionar:
1. Usar ferramenta nativa WebSearch para buscar
2. Usar WebFetch para extrair páginas individuais
3. Reportar no campo erros se nenhuma ferramenta funcionar
```

### 3. Atualizar `skills/dispatch.md`

Adicionar especificações de handoff para o Scout:

```
#### Despacho do Scout (A)

Trigger: Usuário seleciona opção A no menu do Maestro

Envelope de Despacho:
## DESPACHO: SCOUT
### referencia_persona
[Conteúdo completo de personas/scout.md]

### tarefa
Buscar vagas de emprego compatíveis com o perfil do usuário

### perfil_usuario
[Conteúdo de data/user-profile.md]

### contexto
Plataformas alvo: Indeed, Catho, LinkedIn, Glassdoor, Infojobs
Usar Firecrawl CLI para buscas web
Se Firecrawl falhar, usar ferramenta nativa web

### saida_esperada
Arquivo data/job-listings.md com lista de vagas estruturadas como:
- Título
- Empresa
- Localização
- Nível
- Descrição resumida
- URL
- Fonte
```

### 4. Criar template `data/job-listings.md`

Template para armazenar vagas encontradas:

```
## Vagas Encontradas

Data da busca: [data]

Perfil utilizado:
- Função: [valor]
- Nível: [valor]
- Localização: [valor]

### Resumo
Total de vagas encontradas: [N]
Plataformas consultadas: [lista]

### Vagas

(As vagas serão preenchidas pelo Scout no formato abaixo)

### Vaga [N]
Título: [valor]
Empresa: [valor]
Localização: [valor]
Nível: [valor]
Descrição: [valor]
URL: [valor]
Data: [valor]
Fonte: [Indeed|Catho|LinkedIn|Glassdoor|Infojobs]
```

## Esquemas dos Arquivos de Dados

### data/job-listings.md

Gerado pelo Scout após busca de vagas.

```
Data da busca: [YYYY-MM-DD]

Perfil utilizado:
- Função: [valor de user-profile.md]
- Nível: [valor de user-profile.md]
- Localização: [valor de user-profile.md]

### Resumo
Total de vagas encontradas: [N]
Plataformas consultadas: Indeed, Catho, LinkedIn, Glassdoor, Infojobs

### Vagas

### Vaga 1
Título: [valor]
Empresa: [valor]
Localização: [valor]
Nível: [valor]
Descrição: [resumo]
URL: [valor]
Data: [valor ou "Não informada"]
Fonte: [Indeed|Catho|LinkedIn|Glassdoor|Infojobs]

### Vaga 2
...
```

## Fluxo de Integração com o Maestro

```
1. Usuário seleciona opção A (Buscar vagas) no menu do Maestro
   │
   ▼
2. Maestro carrega perfil do usuário (data/user-profile.md)
   │
   ▼
3. Maestro gera Envelope de Despacho para o Scout
   │
   ▼
4. Maestro instrui usuário a copiar envelope para novo chat
   │
   ▼
5. Scout é ativado em novo chat
   │
   ▼
6. Scout lê perfil e constrói queries de busca
   │
   ▼
7. Scout usa Firecrawl para buscar vagas
   │ (firecrawl search "query" --limit N)
   │
   ├─ Se Firecrawl falhar → usar ferramenta nativa web
   │
   ▼
8. Scout extrai dados das vagas encontradas
   │ (firecrawl scrape para cada URL relevante)
   │
   ▼
9. Scout salva resultados em data/job-listings.md
   │
   ▼
10. Scout retorna Envelope de Resposta ao usuário
   │
   ▼
11. Usuário cola resposta no chat do Maestro
   │
   ▼
12. Maestro exibe vagas para o usuário
   │
   ▼
13. Maestro apresenta menu novamente
```

## Testes

- Scout recebe perfil válido e constrói queries corretas
- Scout executa Firecrawl search com sucesso
- Scout extrai dados estruturados das vagas
- Scout salva resultados em job-listings.md no formato correto
- Scout retorna envelope de resposta com estado sucesso
- Se Firecrawl falhar, Scout usa ferramenta nativa web
- Se não encontrar vagas, Scout reporta no campo erros
- Integração com Maestro via envelope de despacho funciona

## Entregável

Scout totalmente funcional com:
- Persona completa (personas/scout.md)
- Skill de busca (skills/job-search.md)
- Template de saída (data/job-listings.md)
- Integração com dispatch.md
- Uso do Firecrawl CLI para buscas web
- Fallback para ferramenta nativa se necessário