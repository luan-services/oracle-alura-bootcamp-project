# Maestro — Orquestrador (Versão Python/Codespaces)

## Visão Geral

Sistema multi-agente construído em Python que auxilia usuários em sua jornada de desenvolvimento de carreira, combinando busca de empregos, identificação de lacunas de habilidades, recomendações de cursos e simulação de entrevistas.

**Objetivo**: Criar o orquestrador principal (`main.py`) que saúda o usuário no terminal, conduz o quiz, salva o perfil em arquivos locais e apresenta o menu de ações.

## Diretrizes de Implementação

- O sistema DEVE ser implementado através de scripts Python. O modelo atuará como um Desenvolvedor Backend escrevendo o código.
- Sem tabelas markdown em nenhuma saída de dados da IA. Use listas numeradas com pares chave-valor para dados estruturados.
- Todos os caminhos de arquivo devem ser relativos à raiz do projeto com prefixo explícito `data/`.
- O gerenciamento de estado será feito via leitura e escrita nativa de arquivos no Python (`open()`, `json`, etc).
- Para simular a comunicação entre agentes, o Python fará chamadas HTTP para o OpenRouter.

## Arquitetura

```text
┌─────────────────────────────────────────────────┐
│                  Usuário (Terminal)             │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│              main.py (Orquestrador)             │
│  - Loop do terminal via input()                 │
│  - Gerencia leitura/escrita em data/            │
│  - Faz requisições HTTP (Dispatcher)            │
└──┬──────────────┬──────────────┬────────────────┘
   │              │              │
   ▼              ▼              ▼
┌─────────┐  ┌──────────┐  ┌──────────────┐
│ SCOUT   │  │ CURATOR  │  │ COACH        │
│ (Busca  │  │ (Busca   │  │ (Simulação   │
│  de     │  │  de      │  │  de          │
│  Vagas) │  │  Cursos) │  │  Entrevistas)│
└─────────┘  └──────────┘  └──────────────┘

```

**Escopo Atual**: Implementar apenas a lógica principal do Maestro (`main.py`) cobrindo o Quiz e o Menu. Os agentes externos serão mockados ou implementados depois.

## Estrutura de Diretórios

```text
recoloca-ia/
├── main.py                     # Script principal (O Maestro)
├── dispatcher.py               # Lógica de chamadas de API (Handoff)
├── personas/
│   └── maestro.md              # Texto base da persona do orquestrador
└── data/
    ├── personality-quiz.json   # Respostas salvas do usuário
    └── user-profile.json       # Perfil consolidado (com funções alvo)

```

## Tasks de Desenvolvimento

### 1. Configurar Estrutura Base

Crie os diretórios `personas/` e `data/`. O estado não será mais salvo em Markdown bagunçado, mas sim em `.json` para facilitar a manipulação pelo Python.

### 2. Criar `personas/maestro.md`

Este arquivo conterá apenas as instruções de comportamento que o Python enviará para a API quando precisar conversar livremente com o usuário. Deve conter o mapeamento completo das funções alvo (ex: Frontend + Júnior = Desenvolvedor Frontend, etc).

### 3. Implementar Lógica de Estado (`main.py`)

Implementar funções no Python equivalentes à ferramenta `find_path`:

* `verificar_quiz_existente()`: Usa `os.path.exists('data/personality-quiz.json')`.
* `salvar_quiz()` e `gerar_perfil_usuario()`: Processa as respostas do terminal e salva os arquivos JSON correspondentes com as funções alvo mapeadas.

### 4. Implementar Fluxo do Terminal (`main.py`)

1. Saudar o usuário via `print()`.
2. Chamar `verificar_quiz_existente()`.
3. Se não existir, iniciar o **Quiz no Terminal**:
* Fazer as 7 perguntas sequencialmente usando `input()`.
* "Qual área mais te anima? (Frontend, Backend, etc)"
* "Como você descreveria seu nível de experiência atual? (Júnior, Pleno, Sênior)"
* "Como você prefere trabalhar? (Remoto, Híbrido, Presencial)"
* "Onde você está localizado?"
* "Quais são suas soft skills mais fortes?"
* "Onde você se vê em sua carreira?"
* "Quais habilidades técnicas você já tem?"


4. Após o quiz, salvar os dados e gerar `data/user-profile.json`.
5. Apresentar o **Menu**:
* **A** — Buscar vagas (Em desenvolvimento)
* **B** — Encontrar cursos (Em desenvolvimento)
* **C** — Praticar entrevista (Em desenvolvimento)
* **D** — Refazer o quiz (Exclui os JSONs e reinicia o loop)



### 5. Preparar Dispatcher (Handoff)

Criar uma estrutura em `dispatcher.py` contendo o "Envelope de Despacho". Este arquivo será responsável por pegar a ação do menu (A, B ou C), carregar o JSON do usuário e montar o prompt formatado que futuramente será enviado via requisição HTTP para a LLM atuar como os outros agentes.

## Testes Esperados ao rodar `python main.py`

* O script não quebra ao iniciar.
* O terminal faz as 7 perguntas e aguarda input do usuário.
* Ao fim do quiz, os arquivos `.json` são criados corretamente na pasta `data/`.
* O menu de opções é exibido e a opção D reinicia o fluxo corretamente.