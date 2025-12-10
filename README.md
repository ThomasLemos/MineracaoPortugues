## Replicação da Metodologia de Geração de Perguntas de Inferência Utilizando Banco de Dados em Portugues
---

## Objetivo e Contexto Científico

Este projeto replica a metodologia de Geração Automática de Perguntas de Inferência Controlada (Controlled Inference Question Generation), 
investigando a capacidade de um Large Language Model (LLM) de gerar itens de Compreensão de Leitura (RC) que alvo um tipo de inferência específico (pronominal bridging), usando técnicas avançadas de prompting.

---
## Informação sobre o codigo

Ambos codigos Scrpits foram feito para rodar no Colab no ambiente T4, o script MineracaoGeracaoDePerguntaPortugues.ipynb vai rodar o codigo e gerar as 5 primeiras pergunta do csv caso queira modificar a quantidade de perguntas geradas na celula 4 modifique MAX_EXAMPLES_TO_PROCESS = 5 para a quantidade desejada.

---

## Fluxo Metodológico Replicado
O experimento segue o fluxo de trabalho de prompting e avaliação proposto.

1. Preparação e Estruturação do Prompt (Few-Shot/CoT)
Taxonomia Alvo: A geração é direcionada ao tipo de inferência: pronominal bridging.

Geração CoT: O LLM é instruído a gerar explicitamente uma seção de REASONING: (Raciocínio) antes da pergunta.

Prompt Estruturado: O prompt final é encapsulado no formato de conversação (<|user|><|assistant|>) e alimentado com o context e o expected_answer do dataset FairytaleQA.

2. Geração de Pergunta (Question Generation - QG) e Detalhes do Modelo
A Geração QG é realizada por um LLM instrucional, simulando o processo de raciocínio.

Modelo Utilizado: Phi-3-mini-4k-instruct (Microsoft).

Características do Modelo:

Tamanho: 3.8 Bilhões de parâmetros.

Otimização: Treinado para seguir Instruções (Instruct), o que o torna ideal para a tarefa de CoT e aderência a regras de formato.

Vantagem: É o maior modelo de código aberto que pode ser rodado de forma viável em GPUs de consumo médio (ou Google Colab), oferecendo um equilíbrio entre inteligência e leveza.

Parsing (RQ3 - Qualidade do Raciocínio): Um módulo de Expressões Regulares (Regex) extrai o generated_question_stem e o generated_reasoning_cot. O sucesso desta extração é crucial para validar a Qualidade do CoT.

3. Classificação e Avaliação do Tipo (RQ2)
Modelo de Classificação: BERT for Sequence Classification (curious008/BertForStorySkillClassification).

Avaliação do Skill (RQ2): O classificador BERT categoriza o item gerado em um dos 7 Narrative Elements (e.g., Character, Setting).

Métrica de Sucesso: A Acurácia do Tipo de Inferência é determinada pela comparação entre o tipo alvo (pronominal bridging) e o predicted_skill_label (classificado pelo BERT).

4. **Armazenamento dos resultados**  
Os resultados são salvos em formato JSON, como mostrado em `example_qg_classification_result_phi3.json`.

--

## Análise de Resultados e Observações
O arquivo de saída (example_qg_classification_result_phi3.json) permite analisar as seguintes métricas, que refletem os achados do artigo base:

Aderência ao Formato (RQ3): Observa-se se a tag generated_reasoning_cot_rq3 foi preenchida corretamente ou se o LLM falhou e gerou "Parsing Geral Falhou." (indicando que o LLM priorizou o conteúdo em detrimento do formato rígido do CoT).

Qualidade da Pergunta (RQ1): Verifica-se se o generated_question_stem_rq1 é uma pergunta sintaticamente válida e coerente com o contexto da história.

Acurácia do Tipo (RQ2): É a comparação entre o target_inference_type (o que pedimos) e o predicted_skill_label (o que o classificador BERT detectou). Desvios altos nesta métrica validam a dificuldade em obter itens com alvo específico.

---

## Referências

Zhu et al. (2025) — Inference Type-based Question Generation for Reading Comprehension Assessment - https://aclanthology.org/2025.bea-1.31.pdf

GEM/FairytaleQA Dataset — https://huggingface.co/datasets/GEM/FairytaleQA
