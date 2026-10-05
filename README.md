# HR Buddy — Assistente Virtual de RH

Projeto desenvolvido para a última edição do **Oracle Next Education (ONE)**, programa realizado pela **Oracle em parceria com a Alura**.

O HR Buddy é um assistente virtual de Recursos Humanos criado no **n8n**. Ele combina inteligência artificial, busca semântica e consulta a banco de dados para responder dúvidas sobre políticas internas e fornecer informações individuais, como saldo de férias e banco de horas.

## Sobre o projeto

O assistente foi criado para a empresa fictícia **ChocolaTech** e atende exclusivamente a assuntos relacionados a RH. A solução utiliza uma base de conhecimento com o manual interno da empresa e, quando o colaborador se identifica, consulta seus dados no MySQL.

Entre as principais capacidades estão:

- responder em português a dúvidas sobre políticas e rotinas de RH;
- consultar informações no manual interno por meio de busca vetorial;
- localizar colaboradores pelo nome completo no banco de dados;
- informar saldos de férias e banco de horas sem inventar dados ausentes;
- manter o contexto da conversa;
- receber e responder mensagens pelo Telegram.

## Arquitetura

O projeto é dividido em três workflows do n8n:

| Workflow | Responsabilidade |
| --- | --- |
| `My_workflow` | Baixa o Manual de RH, gera embeddings e carrega o conteúdo no Vector Store. |
| `My_workflow_2` | Disponibiliza o HR Buddy pelo chat do n8n e conecta o agente à base vetorial e ao MySQL. |
| `My_workflow_3` | Integra o agente ao Telegram, mantendo uma memória separada para cada conversa. |

Fluxo simplificado:

```text
Manual de RH → Cohere Embeddings → Vector Store
                                      ↓
Usuário → Chat/Telegram → Agente de IA → Resposta
                              ↓
                   MySQL (dados individuais)
```

## Tecnologias utilizadas

- [n8n](https://n8n.io/) para automação e orquestração dos workflows;
- [Cohere](https://cohere.com/) para o modelo de linguagem e embeddings;
- modelo de embeddings `embed-multilingual-v3.0`;
- MySQL para armazenar dados dos colaboradores;
- Telegram Bot API como canal de atendimento;
- abordagem RAG (*Retrieval-Augmented Generation*) para consultar o Manual de RH.

## Arquivos do projeto

Os arquivos de workflow deste repositório não contêm credenciais

As credenciais devem ser configuradas diretamente no n8n após a importação.

## Pré-requisitos

- uma instância do n8n com os nós de IA/LangChain disponíveis;
- uma chave de API da Cohere;
- um banco MySQL acessível pela instância do n8n;
- um bot do Telegram, caso o canal do Telegram seja utilizado;
- acesso à internet para carregar o Manual de RH.

## Configuração do banco de dados

O agente consulta a tabela `funcionarios` pelo campo `nome`. Ela deve conter também os campos usados para apresentar o saldo de férias e o banco de horas.

Exemplo de estrutura mínima:

```sql
CREATE TABLE funcionarios (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    saldo_ferias DECIMAL(5,2),
    banco_horas DECIMAL(7,2)
);
```

Adapte os nomes e tipos das colunas ao seu ambiente e às informações esperadas pelo agente.

## Como executar

1. Importe os três arquivos JSON no n8n.
2. Cadastre no n8n as credenciais da Cohere e associe-as aos nós **Cohere Chat Model** e **Embeddings Cohere**.
3. Cadastre a conexão MySQL e associe-a ao nó **Select rows from a table in MySQL**.
4. No workflow de integração com o Telegram, cadastre e associe a credencial do bot aos nós **Telegram Trigger** e **Send a text message**.
5. Execute primeiro o workflow de ingestão (`My_workflow`) para carregar o Manual de RH no Vector Store.
6. Teste o agente pelo chat do n8n com o workflow `My_workflow_2`.
7. Ative o workflow `My_workflow_3` para disponibilizar o atendimento no Telegram.

> **Atenção:** o projeto usa o **Simple Vector Store**, que mantém os dados em memória. Dependendo da configuração e do ciclo de vida da instância do n8n, pode ser necessário executar novamente o workflow de ingestão. Para produção, considere um banco vetorial persistente.

## Segurança e privacidade

- nunca versione chaves de API, senhas, tokens ou arquivos com credenciais;
- gerencie credenciais exclusivamente pelo recurso de credenciais do n8n;
- aplique o princípio do menor privilégio ao usuário do MySQL;
- restrinja o bot e os workflows a usuários autorizados;
- evite registrar dados pessoais em logs;
- utilize dados fictícios em demonstrações e apresentações públicas;
- trate informações de colaboradores de acordo com a LGPD.

## Limitações atuais

- o Vector Store utilizado não é persistente;
- a busca de colaboradores depende do nome informado durante a conversa;
- a solução precisa de controles adicionais de autenticação e autorização antes de um uso real;
- a qualidade das respostas depende da atualização e da cobertura do Manual de RH.

## Contexto educacional

Este projeto consolida conhecimentos desenvolvidos durante a última edição do programa Oracle Next Education, incluindo automação de processos, integração de APIs, bancos de dados, inteligência artificial generativa, embeddings, RAG e criação de agentes conversacionais.

## Autoria

Projeto educacional desenvolvido no programa **Oracle Next Education — Oracle + Alura**.

