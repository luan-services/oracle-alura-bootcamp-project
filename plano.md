# Plano de Desenvolvimento: Orquestrador de Vagas de Emprego

## Visão Geral
Criar um sistema orquestrador que gerencia agentes especializados para buscar vagas de emprego, com foco inicial na criação do agente maestro (orquestrador principal).

---

## 1. Agente Maestro (Orquestrador)

### Descrição
O **Maestro** é o agente principal de comunicação com o usuário. Ele deve:
- Receber demandas e pedidos dos usuários
- Interpretar as necessidades
- Delegar tarefas para agentes específicos usando sua ferramenta nativa de despacho de agentes

### Skill de Delegação
O Maestro deve possuir uma **skill de delegação** que utiliza sua ferramenta nativa de despacho de agentes. Esta skill deve:
- Analisar a solicitação do usuário
- Identificar qual agente especializado é o mais adequado
- Despachar a tarefa para o agente correto
- Receber e consolidar os resultados
- Retornar a resposta formatada ao usuário

---

## 2. Playbook do Maestro

O comportamento do Maestro deve seguir a seguinte sequência:

### Passo 1: Saudação
- Cumprimentar o usuário de forma amigável
- Apresentar-se como o assistente de busca de empregos

### Passo 2: Verificação de Quiz
- Conferir se já existe um **quiz das habilidades e preferências** do usuário salvo no sistema
- Este quiz contém informações sobre o perfil profissional do usuário

### Passo 3: Aplicação do Quiz (se necessário)
Caso não exista o quiz preenchido, o Maestro deve enviar as **5 perguntas** para conhecer o usuário:

1. **Qual é a sua área de atuação profissional ou a área que deseja trabalhar?**
2. **Quais são suas principais habilidades técnicas? (ex: Python, Java, Design, Vendas, etc.)**
3. **Qual é o seu nível de experiência? (Estagiário, Júnior, Pleno, Sênior, Especialista)**
4. **Qual é a sua localização desejada ou preferência de trabalho? (Remoto, Híbrido, Presencial - Cidade/Estado)**
5. **Quais são seus objetivos salariais ou faixa salarial pretendida?**

### Passo 4: Menu de Opções
Após a saudação e verificação/coleta do quiz, disponibilizar um menu de opções:

```
Olá! Como posso ajudar você hoje?

Escolha uma opção:
a) Responder/Atualizar o quiz de perfil
b) Buscar vagas de emprego
```

---

## 3. Primeiro Agente Especializado: Buscador de Vagas

### Descrição
O **Agente Buscador de Vagas** será o primeiro agente especializado a ser criado.

### Funcionalidades (a serem detalhadas em fase posterior)
- Receber parâmetros de busca do Maestro
- Consultar fontes de vagas (APIs, sites de emprego, etc.)
- Filtrar vagas baseadas no perfil do usuário (quiz)
- Retornar lista de vagas relevantes
- Apresentar informações estruturadas das vagas

### Status
⚠️ **Ainda não implementado** - Apenas planejado para desenvolvimento futuro

---

## 4. Próximos Passos

1. Implementar o agente **Maestro** com o playbook definido
2. Criar a skill de delegação no Maestro
3. Implementar o sistema de armazenamento do quiz do usuário
4. Desenvolver o **Agente Buscador de Vagas** (segunda fase)
5. Integrar o Maestro com o Agente Buscador de Vagas
6. Testar o fluxo completo

---

## 5. Observações Técnicas

- O Maestro deve manter contexto da conversa com o usuário
- O quiz deve ser persistido para uso futuro na busca de vagas
- A arquitetura deve permitir adição de novos agentes especializados no futuro
- O sistema de delegação deve ser flexível para rotear diferentes tipos de solicitações