# CitizenTech ColetAr — Global Solution 2026.2

**FIAP — Ciência da Computação | Turma 1CCPI**  
**Disciplina:** Prompt and Artificial Intelligence  
**Professor:** Jorge Luiz Gomes  
**Tema:** CitizenTech — Frente B: Descarte e Sustentabilidade  
**Modalidade:** Trio

## Integrantes

| Integrante | RM | Turma |
|---|---|---|
| Rafael Gandolfi Gonçalves | 569036 | 1CCPI |
| Guilherme Miranda | 573107 | 1CCPI |
| Carlos Eduardo | 572949 | 1CCPI |

> **Aviso:** Vila Aurora, o Programa ColetAr e seus pontos de coleta são fictícios, criados exclusivamente para fins acadêmicos.

## 1. Objetivo

O CitizenTech ColetAr é um assistente de inteligência artificial desenvolvido para ajudar cidadãos a consultar informações sobre descarte de óleo de cozinha usado.

O sistema permite consultar:

- Regras de armazenamento do óleo;
- Quantidade máxima permitida por entrega;
- Gratuidade do serviço;
- Localização dos pontos de coleta;
- Horários de atendimento;
- Interdições e atualizações operacionais.

O projeto utiliza **RAG (Retrieval-Augmented Generation)** para responder com base em documentos, um agente com ferramentas para realizar consultas operacionais, uma interface Gradio e avaliação com RAGAS.

## 2. Arquitetura do sistema

O fluxo principal da aplicação é:

**Usuário → Interface Gradio → Guardrails → RAG ou Agente → Modelo Gemma → Resposta com fontes**

A recuperação documental funciona da seguinte maneira:

**Documentos TXT → Chunking → Embeddings → ChromaDB → Retriever com reranking → Contexto → Prompt LCEL → Ollama Cloud → Resposta**

### Tecnologias utilizadas

| Tecnologia | Finalidade |
|---|---|
| Python | Desenvolvimento do projeto |
| Google Colab | Ambiente de execução |
| LangChain / LCEL | Pipeline RAG e agente |
| ChromaDB | Banco vetorial |
| Ollama local | Execução dos embeddings |
| nomic-embed-text | Modelo de embeddings |
| Ollama Cloud — gemma4:cloud | Geração das respostas |
| Gradio | Interface do chatbot |
| RAGAS 0.4.3 | Avaliação de qualidade |
| Pandas | Organização dos resultados |

## 3. Base documental

A base documental está localizada na pasta `2_base_documental/` e contém dois documentos fictícios, ambos com mais de 100 palavras.

**DOC-01 — Regulamento Municipal Simulado do Programa ColetAr**

Publicado em 10/08/2026, apresenta as regras originais de descarte, limite de cinco litros por morador por visita, gratuidade, endereços e horários.

**DOC-02 — Comunicado Operacional 07/2026**

Publicado em 20/10/2026, atualiza informações dos pontos de coleta.

### Contradições e atualizações

O DOC-02 prevalece sobre o DOC-01 quando modifica informações operacionais:

- **CA-102:** encontra-se interditado por tempo indeterminado.
- **CA-101:** recebe óleo de segunda a sexta-feira, das 9h às 16h.
- **CA-103:** aos sábados, o recebimento de óleo termina às 14h.

Essas contradições permitem testar se o assistente prioriza informações atualizadas, em vez de responder utilizando dados desatualizados.

## 4. Chunking e ChromaDB

Foram comparadas duas estratégias de divisão dos documentos:

| Estratégia | Chunk Size | Overlap |
|---|---:|---:|
| Principal | 500 | 50 |
| Comparativa | 1000 | 100 |

A estratégia principal utiliza chunks menores para favorecer a recuperação de informações específicas, como identificadores dos pontos, horários e regras de descarte.

Os chunks mantêm metadados como `doc_id`, `titulo`, `data_publicacao` e `tema`.

O sistema utiliza busca semântica com ChromaDB e reranking lexical, considerando termos relevantes e códigos dos pontos de coleta.

## 5. Pipeline RAG

O pipeline foi desenvolvido utilizando LangChain Expression Language (LCEL), integrando recuperação documental, contexto, prompt, modelo de linguagem e processamento da resposta.

O assistente foi configurado para:

- Responder com base nos documentos recuperados;
- Identificar as fontes utilizadas;
- Priorizar informações mais recentes;
- Evitar informações inventadas;
- Informar quando não houver evidências suficientes.

### Perguntas testadas

**Pergunta:** O ponto CA-102 está funcionando?

**Resposta esperada:** Não. O ponto está interditado conforme DOC-02.

**Pergunta:** Qual o horário de coleta do CA-101?

**Resposta esperada:** Segunda a sexta-feira, das 9h às 16h, conforme DOC-02.

**Pergunta:** Existe algum ponto de coleta no bairro Bela Vista?

**Comportamento esperado:** Não inventar um ponto de coleta ausente da documentação.

## 6. Agente com ferramentas

O sistema utiliza um agente capaz de decidir quando chamar ferramentas Python.

**Ferramenta 1 — consultar_status_ponto**

Consulta a situação operacional de um ponto de coleta, retornando informações como identificação, nome, status e fonte documental.

**Ferramenta 2 — calcular_proximo_horario**

Calcula o próximo horário de recebimento de óleo conforme dias da semana, horário cadastrado e eventuais restrições operacionais.

O agente registra as ações realizadas, os argumentos utilizados e os resultados das ferramentas.

Foi estabelecido o limite máximo de seis chamadas de ferramentas por pergunta, evitando execuções indefinidas.

## 7. Chatbot — Interface Gradio

O chatbot CitizenTech ColetAr está implementado na **Seção 5 — Interface Gradio** do notebook.

A interface permite conversar com o assistente, consultar informações sobre coleta de óleo e visualizar as fontes documentais utilizadas nas respostas.

### Onde acessar o chatbot

**Google Colab:**

https://colab.research.google.com/drive/1HUTStIobPRC3EFMmAryDg_8XVmubnPx7?usp=sharing

**Repositório GitHub:**

https://github.com/RafaelGandolfiG/global-solution-2sem-IA-1CCPI

### Como iniciar o chatbot

1. Abra o notebook no Google Colab.
2. Configure a chave `OLLAMA_API_KEY` nos Secrets.
3. Execute as células do notebook em ordem, preferencialmente utilizando **Ambiente de execução → Executar tudo (Run All)**.
4. Localize a **Seção 5 — Interface Gradio**.
5. Execute a célula responsável por iniciar a interface, que contém `interface.launch(share=False, prevent_thread_lock=True)`.
6. Utilize o chatbot exibido no ambiente do Colab.

**Importante:** o link do Google Colab permite acessar o notebook, mas o chatbot precisa ser iniciado na sessão de execução. Não se trata de um site permanentemente hospedado.

### Evidência da interface

A captura do chatbot respondendo sobre a interdição do CA-102 está disponível em:

https://github.com/RafaelGandolfiG/global-solution-2sem-IA-1CCPI/blob/main/print_interface_gradio.png

## 8. Segurança e guardrails

Foram implementados quatro mecanismos de segurança:

**1. Controle de escopo:** trata perguntas fora do tema do Programa ColetAr.

**2. Proteção contra prompt injection:** identifica e filtra instruções suspeitas presentes em documentos.

**3. Privacidade e LGPD:** impede o fornecimento de dados pessoais incompatíveis com a finalidade do assistente.

**4. Informação insuficiente:** orienta o sistema a reconhecer quando os documentos não oferecem evidências suficientes.

Esses mecanismos foram demonstrados por testes no notebook. Eles reduzem riscos, mas não representam garantia absoluta contra falhas ou ataques.

## 9. Como executar o projeto no Google Colab

### Pré-requisitos

- Conta Google com acesso ao Colab;
- Conexão com a internet;
- Chave válida da Ollama Cloud;
- Permissão para configurar os Secrets do notebook.

### Instruções

1. Abra o notebook pelo link indicado na Seção 7.
2. No menu lateral do Colab, abra **Secrets**, identificado pelo ícone de chave.
3. Adicione o Secret `OLLAMA_API_KEY` com sua chave pessoal da Ollama Cloud.
4. Autorize o notebook a acessar esse Secret.
5. Clique em **Ambiente de execução → Executar tudo**.
6. Aguarde a instalação das bibliotecas, inicialização do Ollama local e carregamento do modelo de embeddings.
7. O notebook carregará os documentos, criará o índice ChromaDB e executará os testes do RAG, agente, guardrails e RAGAS.
8. A interface do chatbot será disponibilizada na seção correspondente.
9. Ao final da avaliação, o notebook produzirá o arquivo `3_avaliacao_ragas.json`.

### Dependências

O projeto utiliza `ragas==0.4.3` e uma configuração compatível do `langchain-community`.

Durante a instalação no Google Colab, podem aparecer avisos de incompatibilidade entre bibliotecas preinstaladas no ambiente. Nas execuções realizadas pela equipe, esses avisos não impediram a conclusão do Run All.

As versões e os serviços externos podem mudar. Por isso, recomenda-se utilizar a configuração de dependências presente no notebook validado.

### Segurança das credenciais

A chave `OLLAMA_API_KEY` não deve ser publicada no GitHub, no README ou nas saídas do notebook.

Cada avaliador deve configurar sua própria chave nos Secrets do Google Colab.

## 10. Avaliação com RAGAS

A avaliação utiliza oito perguntas:

- Quatro perguntas simples;
- Quatro perguntas relacionadas às contradições e atualizações dos documentos.

Foram avaliadas quatro métricas utilizando o modelo configurado no projeto.

### Resultados da avaliação mais recente

| Métrica | Média |
|---|---:|
| Faithfulness | 0,9688 |
| Answer Relevancy | 0,8833 |
| Context Precision | 1,0000 |
| Context Recall | 0,8750 |

Os resultados foram obtidos pela execução do RAGAS e estão registrados no arquivo `3_avaliacao_ragas.json` correspondente.

### Análise

O Faithfulness elevado indica que as respostas do conjunto avaliado apresentaram boa fundamentação documental.

O Answer Relevancy demonstra que as respostas foram, em geral, pertinentes às perguntas.

O Context Precision elevado indica boa relevância dos contextos recuperados na avaliação.

O Context Recall apresentou uma limitação na pergunta sobre a localização do CA-101, que recebeu pontuação zero nessa métrica, apesar de o endereço constar na documentação.

A pergunta sobre o CA-102 apresentou Faithfulness de 0,75, indicando necessidade de atenção à formulação e fundamentação da resposta.

Esses resultados representam apenas as oito perguntas avaliadas e não garantem desempenho equivalente em todas as situações.

### Possíveis melhorias

Uma evolução do projeto pode comparar diferentes configurações de chunking e reranking, ampliar o conjunto de perguntas, revisar a recuperação de contextos e avaliar a solução com diferentes modelos juízes.

As respostas de referência (`ground_truth`) devem ser elaboradas e validadas pela própria equipe, conforme as regras da disciplina.

## 11. Limitações do projeto

O CitizenTech ColetAr é um protótipo acadêmico baseado em dados fictícios.

Entre suas limitações estão:

- Base documental reduzida;
- Dependência da Ollama Cloud e da internet;
- Necessidade de iniciar o Ollama local;
- Possíveis variações nas respostas do modelo;
- Guardrails que não cobrem todos os tipos de ataque;
- Ausência de integração com sistemas municipais reais.

Uma implantação real exigiria documentos oficiais, atualizações operacionais contínuas, testes ampliados, monitoramento e governança de dados.

## 12. Estrutura da entrega

O pacote final deverá conter:

- `1_Notebook_GS2026_2.ipynb` — implementação completa;
- `2_base_documental/` — documentos fictícios;
- `3_avaliacao_ragas.json` — resultados da avaliação;
- `4_README.md` — documentação;
- `5_certificacao.txt` — evidências, links e identificação;
- `6_Chat_Log.txt` — registro do uso de IA;
- `7_demo_video_url.txt` — link da apresentação em vídeo.



## 13. Uso responsável de inteligência artificial

O ChatGPT foi utilizado como ferramenta de apoio à organização, explicação, desenvolvimento, depuração e documentação do projeto.

A equipe é responsável pela revisão do código, validação das informações, autoria das respostas de referência, interpretação das métricas e apresentação final.

O histórico resumido do apoio da IA está registrado no arquivo `6_Chat_Log.txt`.

---

**CitizenTech ColetAr — FIAP | Global Solution 2026.2 | Turma 1CCPI**