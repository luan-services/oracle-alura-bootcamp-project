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