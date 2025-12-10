## Replicação da Metodologia de Geração de Perguntas de Inferência Utilizando Banco de Dados em Portugues
---

## Objetivo e Contexto Científico

Este projeto replica a metodologia de Geração Automática de Perguntas de Inferência Controlada (Controlled Inference Question Generation), 
investigando a capacidade de um Large Language Model (LLM) de gerar itens de Compreensão de Leitura (RC) que alvo um tipo de inferência específico (pronominal bridging), usando técnicas avançadas de prompting.

---
## Informação sobre o codigo

Ambos codigos Scrpits foram feito para rodar no Colab no ambiente T4, o script MineracaoGeracaoDePerguntaPortugues.ipynb vai rodar o codigo e gerar as 5 primeiras pergunta do csv. 
Caso queira modificar a quantidade de perguntas geradas na celula 4 modifique MAX_EXAMPLES_TO_PROCESS = 5 para a quantidade desejada.

---
## Scripts e Dados

| Item | Nome do Arquivo | Função / Observação |
| :--- | :--- | :--- |
| **Script Principal** | `MineracaoGeracaoDePerguntaPortugues.ipynb` | Implementação completa do *pipeline* QG, classificação e salvamento. Contém o *prompt* **totalmente traduzido** para Português. |
| **Fonte de Dados** | `faquad.csv` | Dataset Faquad-NLI corrigido e filtrado para exemplos de alta qualidade (`label=1`). **Contexto de Entrada:** O texto da `answer` é usado como Contexto (substituto) no *prompt*. |
| **Dataset Base** | `ruanchaves/faquad-nli` | Dataset Original (Hugging Face Hub) utilizado para extração e pré-processamento dos dados QA. |

---

## Fluxo Metodológico Replicado
O experimento segue rigorosamente o fluxo de trabalho de prompting e avaliação proposto.

1. Preparação e Estruturação do Prompt (Few-Shot/CoT)
Taxonomia Alvo: A geração é direcionada ao tipo de inferência: pronominal bridging.

Geração CoT: O LLM é instruído a gerar explicitamente uma seção de RACIOCÍNIO: (Chain-of-Thought) antes da pergunta.

Controle de Idioma: O template Few-Shot/CoT está integralmente em Português, forçando a geração de todo o output no idioma local.

Entrada de Dados: O prompt final é encapsulado no formato de conversação (<|user|><|assistant|>) e alimentado com o Contexto (a resposta do CSV) e o expected_answer.

2. Geração de Pergunta (Question Generation - QG) e Detalhes do Modelo
A Geração QG é realizada por um LLM instrucional, simulando o processo de raciocínio.

Modelo Utilizado: Phi-3-mini-4k-instruct (Microsoft).

Características: 3.8 Bilhões de parâmetros. Otimizado para seguir Instruções (Instruct), ideal para a tarefa de CoT e aderência a regras de formato.

3. Classificação e Avaliação do Tipo (RQ2)
Modelo de Classificação: BERT for Sequence Classification (curious008/BertForStorySkillClassification).

Avaliação do Skill (RQ2): O classificador BERT categoriza o item gerado em um dos 7 Narrative Elements.

Métrica de Sucesso: A Acurácia do Tipo de Inferência é determinada pela comparação entre o tipo alvo (pronominal bridging) e o predicted_skill_label (classificado pelo BERT).

4. Armazenamento dos resultados
Os resultados são salvos em formato JSON (faquad_qg_classification_results.json).

--

## Análise de Resultados e Observações
O arquivo de saída permite analisar as seguintes métricas, que refletem os achados do artigo base:

| Métrica                                  | Implicação Metodológica                                                           | Observações Típicas (Validação)                                                                                                                                                 |
|------------------------------------------|------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aderência ao Formato (RQ3 - Parsing)     | Valida a força do prompt Few-Shot/CoT na replicação da estrutura formal.          | **Alto Sucesso:** O LLM adere às tags `RACIOCÍNIO:` e `PERGUNTA:` em Português corretamente.                                                                                     |
| Qualidade da Pergunta (RQ1 - Coerência)  | Avalia a coerência da pergunta gerada com o Contexto real.                        | **Falha de Coerência (Alucinação):** O LLM falha em aplicar a inferência ao novo Contexto (`CIENCIA_DA_COMPUTACAO`), alucinando um tópico genérico (“biblioteca”) no raciocínio. |
| Acurácia do Tipo (RQ2 - Desvio)          | Avalia se a pergunta gerada corresponde ao tipo de inferência alvo.               | **Desvio Alto:** Confirma a dificuldade do método: a pergunta gerada não corresponde ao tipo de inferência alvo, sendo classificada como *Outcome Resolution*.                   |


---

## Referências
Dataset Original (Hugging Face Hub) - https://huggingface.co/datasets/ruanchaves/faquad-nli

Zhu et al. (2025) — Inference Type-based Question Generation for Reading Comprehension Assessment - https://aclanthology.org/2025.bea-1.31.pdf

GEM/FairytaleQA Dataset — https://huggingface.co/datasets/GEM/FairytaleQA
