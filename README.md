# d3js-proj

## 1. Coleta de Dados

Primeiro, você vai precisar de dados do repositório Open Source que deseja analisar. Isso pode ser feito via API do GitHub. Aqui estão os dados que você pode obter:

Commits: Número de commits por dia/mês/ano.
Issues: Quantidade de issues abertas/fechadas ao longo do tempo.
Pull Requests: Contribuições feitas e aceitas/rejeitadas.
Contribuidores: Quem são os contribuidores mais ativos, com suas contribuições ao longo do tempo.
Ferramenta: Use a GitHub REST API ou a GitHub GraphQL API para extrair esses dados. Para começar, crie uma chave de acesso na sua conta GitHub e faça chamadas para os endpoints que trazem as informações de commit, issues e pull requests.

## 2. Preparação dos Dados

Depois de coletar os dados, organize-os em um formato que o D3.js possa consumir facilmente, como JSON ou CSV. Aqui está um exemplo de como estruturar os dados:

```json
{
  "date": "2023-01-01",
  "commits": 10,
  "issuesOpened": 5,
  "issuesClosed": 3,
  "pullRequests": 2,
  "contributors": 3
}
```
Você pode agrupar os dados por mês, ano, ou até por semana, dependendo do nível de detalhe que deseja mostrar.

## 3. Design da Visualização

D3.js oferece muitas possibilidades para visualizações dinâmicas e interativas. Aqui estão algumas ideias:

Gráfico de Linhas para mostrar o número de commits, issues abertas/fechadas, e pull requests ao longo do tempo.
Gráfico de Barras para comparar a atividade de diferentes contribuidores ou o número de pull requests aceitos por contribuidores.
Diagrama de Voronoi ou Scatterplot para mostrar a frequência de contribuições versus tempo.
Heatmap para visualizar atividades (commits, issues) por dia da semana ou hora do dia, para identificar padrões de contribuições.
Ferramenta: Use as funções de escalas de D3.js (d3.scaleTime, d3.scaleLinear) para mapear os dados no gráfico e renderizar com d3.line ou d3.bar.

## 4. Interatividade

Adicione interatividade para tornar a visualização mais envolvente:

Tooltip: Quando o usuário passar o mouse sobre um ponto no gráfico, você pode mostrar mais detalhes (ex.: número de commits em um dia específico).
Filtros: Permita que o usuário selecione quais dados visualizar (ex.: commits apenas, ou comparar issues abertas e fechadas).
Zoom e Pan: Utilize zoom para explorar períodos específicos com mais detalhes.
Ferramenta: A função d3.zoom() vai ajudar a implementar zoom e pan na visualização.

## 5. Implementação

Com os dados prontos e a estrutura da visualização planejada, comece a implementar:

Setup básico: Carregue os dados via AJAX usando d3.json() ou d3.csv().
Escalas: Configure as escalas de tempo (d3.scaleTime) e de valores (d3.scaleLinear) para mapear os dados no gráfico.
Eixos: Adicione eixos temporais (d3.axisBottom) e numéricos (d3.axisLeft) para guiar a visualização.

Renderização: Use as funções d3.line() ou d3.bar() para desenhar as séries de dados no gráfico.
## 6. Ajustes e Estilo

Depois da visualização básica, ajuste os estilos, cores e tipografia para torná-la mais atraente. D3.js permite personalizar quase todos os aspectos visuais.

## 7. Deploy

Por fim, você pode hospedar o projeto em um ambiente como GitHub Pages ou Netlify, facilitando a visualização pública. Isso permite que outras pessoas interajam com os dados e visualizações diretamente no navegador.

<hr>

## 1. GitHub REST API

A GitHub REST API é uma maneira simples de acessar informações detalhadas de repositórios. Para obter o número de commits de um repositório específico, você pode usar o endpoint de commits.

Endpoint para Commits:
Para obter a lista de commits de um repositório:

bash
Copiar código
GET /repos/{owner}/{repo}/commits
Por exemplo, para acessar os commits do repositório facebook/react, você pode fazer uma requisição:

ruby
Copiar código
GET https://api.github.com/repos/facebook/react/commits
Passo a Passo para Coletar Dados:
a) Criar uma API Key (Opcional, mas recomendado)
Acesse seu perfil no GitHub.
Vá até Settings > Developer Settings > Personal Access Tokens.
Gere um token com as permissões necessárias para acessar os repositórios públicos.
Use esse token na sua requisição para evitar limitações de taxa de uso.
b) Exemplo de Requisição em JavaScript (com Fetch)
Aqui está um exemplo de como você pode usar fetch() em JavaScript para acessar o número de commits:

javascript
Copiar código
const owner = 'facebook'; // Proprietário do repositório
const repo = 'react';     // Nome do repositório

fetch(`https://api.github.com/repos/${owner}/${repo}/commits`, {
  headers: {
    'Authorization': 'token SEU_TOKEN_AQUI' // Opcional, mas evita limite de requisições
  }
})
  .then(response => response.json())
  .then(data => {
    console.log('Total de commits:', data.length);
    console.log(data); // Lista de commits
  })
  .catch(error => console.error('Erro:', error));
  
## 2. GitHub GraphQL API

A GraphQL API é mais flexível, pois permite que você consulte exatamente os dados que precisa, reduzindo a quantidade de informações desnecessárias.

Exemplo de Consulta para Obter Commits:
Você pode fazer uma consulta em GraphQL para buscar dados como commits por data, nome dos contribuidores e outras estatísticas.

graphql
Copiar código
{
  repository(owner: "facebook", name: "react") {
    object(expression: "main") {
      ... on Commit {
        history(first: 100) {
          edges {
            node {
              committedDate
              author {
                name
              }
            }
          }
        }
      }
    }
  }
}
Essa consulta retorna os últimos 100 commits no branch principal do repositório facebook/react, incluindo a data e o autor de cada commit.

Passo a Passo com a API GraphQL:
a) Autenticação
Gere um token de acesso pessoal no GitHub (como mencionado anteriormente).
Use esse token para autenticar suas requisições GraphQL.
b) Requisição em JavaScript
Aqui está um exemplo de como você pode fazer uma requisição para a API GraphQL usando fetch:

javascript
Copiar código
const query = `
  {
    repository(owner: "facebook", name: "react") {
      object(expression: "main") {
        ... on Commit {
          history(first: 100) {
            edges {
              node {
                committedDate
                author {
                  name
                }
              }
            }
          }
        }
      }
    }
  }
`;

fetch('https://api.github.com/graphql', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer SEU_TOKEN_AQUI`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ query })
})
  .then(response => response.json())
  .then(data => console.log('Commits:', data.data.repository.object.history.edges))
  .catch(error => console.error('Erro:', error));
  
## 3. Filtrar por Data

Para limitar os commits a um intervalo de datas, você pode usar parâmetros adicionais na REST API, como since e until. Exemplo:

javascript
Copiar código
fetch(`https://api.github.com/repos/facebook/react/commits?since=2023-01-01T00:00:00Z&until=2023-12-31T23:59:59Z`)
Isso retorna apenas os commits feitos entre essas duas datas.

Conclusão
Usando a API do GitHub, você pode facilmente coletar dados de commits de um repositório Open Source. A REST API é mais simples, mas a GraphQL API oferece mais flexibilidade. Ambas são poderosas para coletar e visualizar a evolução de um projeto!
