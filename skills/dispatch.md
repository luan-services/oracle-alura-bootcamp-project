#Protocolo de Despacho e Handoff de Agentes

## Tabela de Roteamento

**Opção A** → Agente: Scout | Função: Busca de vagas (Indeed, Catho, LinkedIn, Glassdoor, Infojobs)
**Opção B** → Agente: Curator | Função: Busca de cursos para preencher lacunas de habilidades (Alura)
**Opção C** → Agente: Coach | Função: Simulação de entrevistas (6 despachos sequenciais)
**Opção D** → Agente: Maestro | Função: Refazer o quiz (tratar diretamente)

## Formato de Envelope de Despacho

O Maestro constrói este prompt e pede para o usuário copiá-lo para um novo chat.

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

