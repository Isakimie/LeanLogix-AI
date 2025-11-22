# 🚛 LeanLogix AI: Automação de Análise de Causa Raiz (RNC)

> Solução de **Automação Serveless com IA generativa** criada para o desafio "Build Your First Copilot Challenge - Foundry Edition" da Azure Frontier Girls. O objetivo central é aplicar a **IA Generativa do Azure AI Foundry** para otimizar fluxos de trabalho na **Gestão da Qualidade e Logística**. O projeto transforma o processo lento e manual de Registro de Não-Conformidade (RNC) em uma **análise estruturada e imediata**, acelerando a identificação da Causa Raiz e o ciclo de **Melhoria Contínua (Kaizen)**.


 
## 🎯 O Problema
Na logística e na indústria, o tratamento de **Não-Conformidades (RNC)** — como atrasos, avarias ou erros de expedição — muitas vezes sofre com:
* **Lentidão:** Tempo gasto redigindo relatórios técnicos.
* **Subjetividade:** Descrições vagas que dificultam a identificação da causa real.
* **Falta de Metodologia:** Esquecimento da aplicação de ferramentas de qualidade (como Ishikawa).


## 💡 A Solução - Agente de Análise de RNC (LeanLogix AI)
A solução é um agente de automação inteligente (Copilot) que atua como um "Assistente de Qualidade Virtual" eliminando a subjetividade e a lentidão na criação de relatórios de Não-Conformidade.

O sistema realiza os seguintes processos:

1.  **Entrada Simplificada:** Recebe o relato informal de um incidente logístico (o input do sistema).
2.  **Análise Profunda:** Utiliza o modelo de **IA Generativa do Azure** (GPT-4.1-mini) para processar o texto, aplicando o conhecimento em **Lean Six Sigma**.
3.  **Output Estruturado:** O agente transforma a linguagem natural em um documento técnico estruturado, sugerindo a Causa Raiz (classificada por tipo de **Ishikawa** — Máquina, Método, Material, Mão-de-obra, etc.) e uma **Ação Corretiva Imediata**.
4.  **Entrega Serverless:** O resultado final é entregue automaticamente via e-mail para o gestor responsável, garantindo a rastreabilidade e a ação rápida.

Esta arquitetura Serverless (Logic Apps) assegura que a solução tenha um custo operacional mínimo, escalabilidade e seja totalmente compatível com o ambiente Azure.


## 🛠️ Tecnologias Utilizadas
Este projeto foi construído inteiramente no ecossistema do Microsoft Azure, utilizando uma arquitetura **Serverless** e de baixo custo, ideal para a gestão eficiente de recursos na conta de desenvolvimento.

| Categoria | Componente e Detalhe | Finalidade no Projeto |
| :--- | :--- | :--- |
| **I. Inteligência Artificial (IA)** | **Azure OpenAI Service (no Azure AI Foundry)** | Serviço de hosting e gestão dos modelos de IA no Azure. |
| | **Modelo GPT-4.1-mini (Deployment)** | O *cérebro* do agente. Responsável por interpretar a linguagem natural, aplicar o raciocínio Lean Six Sigma e gerar a análise de Causa Raiz. |
| **II. Automação e Orquestração** | **Azure Logic Apps (Consumption Plan)** | A ferramenta *Serverless* escolhida para orquestrar o fluxo de trabalho (substituindo o Power Automate devido à otimização de custos e recursos na conta gratuita). |
| | **Gatilho (HTTP Request)** | Recebe o input (o relato do problema) e inicia o processo de automação. |
| | **Ação HTTP (Plano B)** | Executa a chamada direta à API REST do Azure OpenAI, demonstrando conhecimento em integração de APIs. |
| **III. Manipulação e Saída** | **Parse JSON** | Limpa e transforma a resposta técnica bruta da API do GPT em dados estruturados e utilizáveis. |
| | **Connector de E-mail (Outlook/Gmail)** | Envia a notificação final com o relatório de RNC gerado pela IA. |
| **IV. Metodologia e Governança** | **JSON & Prompt Engineering** | Utilizado para definir o esquema de entrada e para instruir a IA a atuar como um especialista Lean Six Sigma Black Belt. |
| | **Resource Group & Tags** | Utilizado para organização, controle de custos e governança dos recursos (ex: `purpose: automatizar-causa-raiz`). |

## ⚠️ Errata: Escopo e Adaptação Técnica

O escopo inicial deste projeto previa a integração e a coleta de informações em tempo real de uma aplicação Python, simulando um *feed* contínuo de dados de telemetria ou sistemas de gestão (ERP).

Devido às limitações de recursos, cotas e políticas de acesso impostas a contas não-empresariais no Azure (Free Tier), que restringem o uso contínuo de recursos de computação e o acesso irrestrito a APIs de IA:

* **O Plano:** A coleta de dados em tempo real foi abandonada.
* **O Foco:** O projeto foi pivotado para o modo **Intelligent Process Automation**.

Com isso, o valor do projeto foi maximizado no **Processamento de Dados de Alto Valor (Conhecimento)**, utilizando o **Logic App** como orquestrador para receber o *input* por um gatilho manual e focar 100% na aplicação da metodologia **Lean Six Sigma** pela IA. Esta adaptação garantiu a entrega estável da Análise de Causa Raiz dentro das restrições do desafio.

## 🔄 Arquitetura do Fluxo
1.  **Input (Gatilho HTTP):** O sistema recebe um JSON com o relato do problema.
2.  **Processamento (Azure OpenAI):** Envio do relato via requisição HTTP POST para o modelo GPT.
3.  **Inteligência:** O modelo analisa o texto e gera um JSON estruturado contendo:
    * Resumo Técnico
    * Análise de Ishikawa (Causa Raiz provável)
    * Sugestão de Ação Corretiva
4.  **Tratamento (Parse JSON):** O Logic App interpreta a resposta da IA.
5.  **Output (E-mail):** Envio automático do relatório formatado para o gestor da área.

  ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/8ab41ff58c7ecc669efcb20b5d0dec2b79986736/Arquitetura_do_Fluxo.png)

## ▶️ Guia de Implementação (Passo a Passo Detalhado)

### 🧠 Fase 1: Preparar a Inteligência (Azure AI Foundry)

Nesse primeira fase vamos criar a inteligência artificial e configurá-la para agir como o especialista em logística.

1.  **Criar Grupo de Recursos (RG):**
    * Entre no **Portal do Azure** e pesquise por `Resource groups`.
    * Clique em **+ Create**.
    * **Resource group:** `rg-automacao-causa-raiza-logistica`
    * Region: **Sweden Central** (escolha exatamente esta, pois é a região com maior disponibilidade de cota para modelos de IA)
    * **Tags:** Adicione `Name: purpose` e `Value: automatizar-causa-raiz`.
    * Clique em **Review + create** e depois em **Create**.

   ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/802cff98995992631d0c4c22eae0e6fc3fb0f5a5/01_Create_a_Resource_Group.png)

    
3.  **Criar o Azure OpenAI:**
    * Na busca do topo, digite `Azure OpenAI` e selecione **+ Create**.
    * **Subscription:** Selecione a sua (Free Trial/Azure for Students).
    * **Resource group:** `rg-automacao-causa-raiza-logistica` (o mesmo que você acabou de criar)
    * **Region:** **Sweden Central** (importante manter a mesma)
    * **Name:** `oai-logistica`
    * **Pricing tier:** Escolha **Standard S0**.
    * **Tags:** Adicione `Name: purpose` e `Value: automatizar-causa-raiz`.
    * Clique em **Next** até chegar em **Review + create** e finalize. (pode demorar uns minutos).
      
   ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/1bf95b45c714ac425fce461d1d8579042094d08a/02_Create_Azure_OpenAI.png)
   
   * Clique em **Create budget** (é importante limitar budget, caso tenha criado um conta gratuita e recebeu $200 em créditos, assim pode receber alertas de gastos)
      
   ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/802cff98995992631d0c4c22eae0e6fc3fb0f5a5/03_Create_Budget.png)


3.  **Pegar Chaves (Endpoint e API Key):**
    * Vá para o recurso `oai-logistica`.
    * No menu à esquerda, vá em **Keys and Endpoint**.
    * **Copie o "KEY 1" e o "Endpoint" (URL)** e salve-os em um bloco de notas (serão usados na requisição HTTP).
      
4.  **Implantar o Modelo (Deploy no Foundry):**
    * Na tela "Overview", clique em **"Go to Foundry portal"**.
    * No menu esquerdo, clique em **Deployments**.
    * Clique em **+ Create new deployment**.
    * **Select a model:** `gpt-4.1-mini`
    * **Model version:** `Global Standard`
    * **Deployment name:** `gpt-4.1-mini`
    * **Tokens per Minute Rate Limit:** `100k`
    * Clique em **Create**.

  ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/802cff98995992631d0c4c22eae0e6fc3fb0f5a5/04_Deploy_gpt-4.1mini.png)

---

### ⚙️ Fase 2: Criar a Automação (Logic App)

Esta fase configura o fluxo que utiliza o método **HTTP** para comunicar com a IA.

1.  **Criar o Logic App (Consumption):**
    * Busque por `Logic Apps` e clique em **+ Create**.
    * **Hosting option:** **Consumption** (modelo Serveless ao qual cobra apenas quando utilizado)
    * **Resource Group:** `rg-automacao-causa-raiza-logistica`
    * **Logic App name:** `logistica_flow`
    * **Region:** **Sweden Central**
    * **Tags:** Adicione `Name: purpose` e `Value: automatizar-causa-raiz`.
    * Clique em **Review + create** e depois em **Create**.
    * Quando terminar, clique em **Go to resource**.

   ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/0b07f98427211a7ee0b256b7513167e1bfbf9ac2/05_Create_Logic_App.png)
      
2.  **Passo A: O Gatilho (Trigger):**
    * Ao abrir o Logic App, selecione **"When a HTTP request is received"**.
    * Em **Request Body JSON Schema**, cole o seguinte código:
        ```json
        {
            "type": "object",
            "properties": {
                "relato": {
                    "type": "string"
                }
            }
        }
        ```
    ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/5fc1aaa70eb78f9320377f1f4a8d958c2b11fbf8/06_Logic_App_Designer_HTTP.png)
    
4.  **Passo B: Conectar a IA (Ação HTTP):**
    * Clique em **+ New Step**. Na aba **Built-in**, selecione **HTTP**.
    * **Method:** `POST`
    * **URI:** Insira o seu Endpoint completo (substituindo seu link base e nome de Deployment):
        ```text
        [https://oai-logistica.openai.azure.com/openai/deployments/gpt-4.1-mini/chat/completions?api-version=2024-02-15-preview](https://oai-logistica.openai.azure.com/openai/deployments/gpt-4.1-mini/chat/completions?api-version=2024-02-15-preview)
        ```
    * **Headers (Cabeçalhos):**
        * Adicione `Content-Type` com valor `application/json`.
        * Adicione `api-key` com valor sua **KEY 1**.
    * **Body (Corpo):** Cole o JSON abaixo. O `content` já está usando a expressão de Conteúdo Dinâmico:
      


        ```json
        {
          "messages": [
            {
              "role": "system",
              "content": " Você é um especialista em Logística Lean Six Sigma. Analise o relato do problema abaixo e gere um resumo técnico, identifique a causa provável (Ishikawa: Máquina, Método, Mão-de-obra ou Material) e sugira uma ação corretiva imediata.”"
            },
            {
              "role": "user",
              "content": "@{triggerBody()?['relato']}"
            }
          ],
        "temperature": 0.7
        }
        ```

     ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/e37fa84bd9ce6772d5290aabd0573fe8b2fcef94/07_Logic_App_Designer_HTTP.png)
        
5.  **Limpeza de Dados antes de enviar o e-mail (Parse JSON):**
    * Clique em **+ New Step**. Na aba **Built-in**, selecione **Parse JSON**.
    * **Content:** Selecione **Body** da ação HTTP anterior.
    * **Schema:** Clique no link azul "Use sample payload to generate schema" e cole este código:
        ```json
        {
            "choices": [
                {
                    "message": {
                        "content": "Texto da resposta aqui"
                    }
                }
            ]
        }
        ```

     ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/1c36cd18fe26c24b2ab0b866da3763c51153b30b/08_Sample_JSON_payload.png)
      
6.  **Passo C: Enviar o E-mail:**
    * Clique em **+ New Step**. Busque por **"Outlook"** ou **"Gmail"** e selecione **"Send an email (V2)"**.
    * Configure sua conta de e-mail.
    * **To (Para):** Seu próprio e-mail (apenas para teste)
    * **Subject (Assunto):** `Relatório Automático de RNC`
    * **Body (Corpo):** Insira o conteúdo de forma formatada.
    * Exemplo: `Olá, sou seu Agente Lean, segue a análise do problema reportado:` Dê um Enter.
    * Na janelinha flutuante dinâmica, procure a seção do "Body content” no Parse JSON.`
    * Clique em **Save**.

    ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/4bcb52f62c69c9291199019ce933e45ac9903e13/09_Send_an_Email.png)

---

### 🧪 Fase 3: Teste

1.  No Designer, clique no botão **Run** e escolha **Run with payload**.
2.  No campo **Body**, cole o JSON de teste:
    ```json
    {
        "relato": "A empilhadeira quebrou no armazém 3 e atrasou o carregamento do caminhão da transportadora ABC em 2 horas. O motorista ficou bravo."
    }
    ```
3.  Clique em **Run** e verifique seu e-mail para o relatório de RNC gerado pela IA.

    ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/cf5fd2cab81017d7c6d500554864262c2b81f581/10_Run_with_Payload.png)

## 📸 Evidências de Funcionamento

### 1. O Fluxo no Logic Apps

   ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/5fc1aaa70eb78f9320377f1f4a8d958c2b11fbf8/11_Fluxo_com_%20'checks-verdes'.png)

### 2. Exemplo de Saída (E-mail Recebido)

   ![image alt](https://github.com/Isakimie/LeanLogix-AI/blob/1cf63fc23c39c17a7c423561263500d02293ff11/12_Email_Recebido.png)

## 🚀 Próximos Passos (Melhorias Futuras)

Este projeto foi desenhado para ser um Mínimo Produto Viável (MVP) funcional. Para levá-lo ao ambiente de produção (Enterprise), as seguintes melhorias seriam implementadas:

* **Front-end para o Usuário (Power Platform):** Substituir o gatilho HTTP manual por uma aplicação *Low-Code* no **Power Apps** ou **Power Automate**. Isso permitiria que operadores e gestores submetessem os relatos de incidentes via um formulário amigável no celular ou tablet, eliminando o uso de URLs e JSON.
* **Data & Analytics (Power BI):** Integrar o fluxo do Logic App com um **Azure Storage Account** (Data Lake) ou **Azure SQL/Cosmos DB** para armazenar todas as análises de RNC. Posteriormente, criar um dashboard no **Power BI** que gere gráficos de Pareto e monitore as Causas Raízes mais frequentes por região ou tipo de avaria.
* **Formalização do Relatório:** Usar o **Azure AI Document Intelligence** para gerar um PDF ou documento formal (A3 Report) com base na análise de IA. Isso garantiria que o output fosse estruturado e pronto para arquivamento digital, cumprindo requisitos de **ISO 9001**.
* **Monitoramento Ativo:** Integrar o fluxo a sistemas de notificação em tempo real, como **Microsoft Teams** ou **Azure Event Grid**, para que o alerta da RNC seja enviado para a equipe de qualidade no momento exato em que a falha é detectada pelo ERP/WMS.

## 📚 Referências

Abaixo estão os links de referência utilizados, demonstrando o alinhamento com a documentação oficial da Microsoft e as tecnologias do Azure AI Foundry.

| Categoria | Justificativa Técnica | Link |
| :--- | :--- | :--- |
| **1. Plataforma AI (Foundry)** | Documentação principal sobre o Azure AI Studio, validando o uso do ecossistema de gestão e implantação dos modelos de IA. | [O que é o Azure AI Studio (Foundry)?](https://learn.microsoft.com/pt-br/azure/ai-studio/) |
| **2. Configuração de IA** | Guia de como criar o recurso OpenAI e gerenciar a cota de implantação de modelos (como o GPT-4.1-mini) no Azure. | [Implementar um recurso do Serviço OpenAI do Azure](https://learn.microsoft.com/pt-br/azure/ai-services/openai/how-to/create-resource?pivots=web-portal) |
| **3. Orquestração (Logic Apps)** | Justifica a escolha do Azure Logic Apps (modelo Consumption/Serverless) como o orquestrador do fluxo. | [Visão Geral dos Aplicativos Lógicos do Azure](https://learn.microsoft.com/pt-br/azure/logic-apps/logic-apps-overview) |
| **4. Gatilho (HTTP Request)** | Valida o uso do gatilho "Quando uma solicitação HTTP é recebida" e a importância do Schema JSON para receber dados. | [Receber e responder a chamadas HTTPS de entrada no Azure Logic Apps](https://learn.microsoft.com/pt-br/azure/logic-apps/logic-apps-http-endpoint) |
| **5. Integração Avançada** | Referência técnica da API REST para *Chat Completions* que foi usada na ação HTTP, demonstrando conhecimento em integração de baixo nível. | [Referência da API REST do Serviço OpenAI do Azure](https://learn.microsoft.com/pt-br/azure/ai-services/openai/reference) |
| **6. Tratamento de Dados** | Guia sobre a ação `Parse JSON`, que foi essencial para transformar a resposta bruta da IA em dados estruturados e utilizáveis. | [Executar operações de dados no Azure Logic Apps (Analisar JSON)](https://learn.microsoft.com/pt-br/azure/logic-apps/logic-apps-perform-data-operations#parse-json-action) |
| **7. O Desafio** | Link direto para o repositório do desafio, fornecendo o contexto de origem do projeto. | [Azure Frontier Girls - AI Challenge](https://github.com/Miyake-Diogo/AzureFrontierGirls-AI-Challenge) |
  
---
**Desenvolvido por Isabela Kimie Ota**
*Engenheira Mecânica | Lean Six Sigma |  IA Aplicada à Melhoria Contínua*
[https://www.linkedin.com/in/isabelaota/]


