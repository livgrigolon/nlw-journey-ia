# Travel Agent API - Lambda Function

Este projeto implementa uma função AWS Lambda que utiliza a biblioteca LangChain e a API OpenAI para gerar roteiros de viagem personalizados. A função permite ao usuário fornecer uma pergunta sobre sua viagem e, em resposta, ela gera um roteiro detalhado, incluindo eventos relevantes e preços de passagens, utilizando fontes de dados da web.

## Funcionalidades

- Gera roteiros de viagem personalizados com base no destino, data e eventos relevantes.
- Busca informações da web utilizando ferramentas como DuckDuckGo e Wikipedia.
- Processa e organiza documentos da web para fornecer contexto adicional em roteiros de viagem.
- Integração com o AWS Lambda para deploy escalável e acessível via API HTTP.

## Tecnologias Utilizadas

- **Python 3.12** (Executado em ambiente Lambda)
- **AWS Lambda**: Para hospedar e executar a função de maneira escalável.
- **LangChain**: Para orquestração de consultas e processamento de linguagem natural.
- **OpenAI GPT-3.5-turbo**: Para processamento de linguagem e geração de conteúdo.
- **Chroma**: Para armazenar e recuperar documentos relevantes em vetores.
- **BeautifulSoup**: Para extração de dados de páginas web.

## Arquivos Principais

- **`Dockerfile`**: Contém a configuração da imagem Docker usada para a função Lambda.
- **`lambda_handler.py`**: Script principal que define a função Lambda e seus subcomponentes.
- **`requirements.txt`**: Arquivo com as dependências Python necessárias para o projeto.
- **`travelAgent.py`**: Implementação do agente de pesquisa e criação de roteiros.

## Requisitos de Instalação

### Pré-requisitos

- Docker
- AWS CLI configurado
- Chave API da OpenAI

### Como rodar localmente

1. **Clone o repositório:**
   ```bash
   git clone https://github.com/seu-usuario/seu-repositorio.git
   cd seu-repositorio

2. **Configurar variáveis de ambiente:** Certifique-se de que você tem a chave da OpenAI configurada:
    ```bash
    export OPENAI_API_KEY='sua-chave-api-openai'

3. **Build da imagem Docker:** Use o Docker para criar uma imagem da função Lambda:
   ```bash
   docker build -t travel-agent-api .

4. **Rodar a imagem localmente:** Após construir a imagem, você pode rodar o contêiner:
    ```bash
    docker run -p 9000:8080 travel-agent-api

5. **Testar localmente:** Agora você pode testar a função localmente via curl:
    ```bash
    curl -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" \
    -d '{"question": "Vou viajar para Londres em agosto de 2024. Quero que faça um roteiro para mim com eventos que ocorrerão na data e com preços de passagem de São Paulo para Londres."}'

### Deploy para AWS Lambda

1. **Configurar as credenciais AWS CLI:**
    ```bash
    aws configure
2. **Autenticar no Amazon ECR:** Execute o comando para autenticar no Elastic Container Registry.
   ```bash
   aws ecr get-login-password --region <sua-regiao> | docker login --username AWS --password-stdin <url-do-ecr>
3. **Criar repositório ECR:** Se você ainda não tiver um, crie um repositório ECR:
   ```bash
   aws ecr create-repository --repository-name travel-agent-api
4. **Fazer push da imagem:** Faça o push da sua imagem para o ECR:
    ```bash
    docker tag travel-agent-api:latest <url-do-ecr>:latest
    docker push <url-do-ecr>:latest
5. **Deploy para Lambda:** Configure sua função Lambda para usar a imagem do ECR com a AWS Console ou CLI.

## Como Funciona

1. Agente de Pesquisa (`researchAgent`): Faz buscas na web com base no input do usuário utilizando ferramentas como DuckDuckGo e Wikipedia.
2. Carregamento de Dados (`loadData`): Extrai conteúdo relevante de sites e o armazena em um vetor utilizando Chroma.
3. Agente Supervisor (`supervisorAgent`): Combina os resultados das buscas web e os documentos relevantes para gerar um roteiro de viagem personalizado.
4. Lambda Handler: Processa a requisição recebida via API, executa a lógica do agente e retorna o roteiro gerado.
