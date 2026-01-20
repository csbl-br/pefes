# PEFES — Prompt Engineering for Epidemiological Surveillance  
**LLM Framework for Reproducible and Validated Epidemiological Analyses Based on Data Dictionaries**

## Overview

This repository contains the core specification of a prompt-engineering–based system designed to support epidemiological data analysis using Large Language Models (LLMs). The system interprets natural language questions about Brazilian public health surveillance databases (e.g., SINAN, SIVEP) and generates analytical scripts strictly grounded in predefined data dictionaries.

The system is designed to minimize hallucination, enforce methodological rigor, and ensure reproducibility by constraining the LLM to validated variables, datasets, and analytical scopes.

## How to Use

The system is based on a set of core instructions (`pefes_instructions.json`) that define the normative behavior of the LLM, including validation rules, analytical scope, and strict constraints against inventing variables, categories, or data. These instructions constitute the invariant core of the system.

In addition, the model must be provided with the data dictionary corresponding to the epidemiological database to be analyzed (for example, the SINAN Dengue data dictionary). This dictionary formally describes the available variables, their types, domains, and meanings, and serves as the sole authorized reference for analytical script generation.

The core instructions and data dictionaries **must be loaded into the LLM context** prior to user interaction. This can be achieved through different mechanisms, such as:

- **Direct file upload** (e.g., JSON or Markdown files) into the model context;
- **Prompt injection** via system messages containing the instruction and dictionary contents;
- **Retrieval-Augmented Generation (RAG)**, in which an external component retrieves version-controlled artifacts from the repository and supplies the relevant content to the model during inference.

In all cases, the LLM **does not autonomously access external repositories or the internet**. All normative and structural knowledge used by the system must be explicitly provided within the interaction context.

## System Principles

The system operates under the following fundamental principles:

* Dictionary-driven reasoning: the LLM may only reference variables, categories, and structures explicitly defined in the active data dictionary.

* No data invention: the system never assumes, simulates, or fabricates data, variables, or results.

* No external data access: all information used by the LLM must be provided via this repository or by the user.

* Explicit validation: all requests are validated for scope, feasibility, and completeness before script generation.

* Clarification over assumption: ambiguous or incomplete questions trigger clarification requests rather than implicit assumptions.

## Repository Structure

```
pefes_instructions.json       # Core system rules and behavior
/dictionaries
  └── *.json                  # Dataset-specific data dictionaries
README.md
```

## System Workflow

1. Question interpretation: the system parses the user’s natural language query.

1. Context validation: disease, period, dataset, and programming language are checked.

1. Variable planning: only variables present in the active dictionary are selected.

1. Script generation: an analytical script is generated following best practices.

1. Fallback handling: if constraints are violated, standardized feedback is returned.

**The system does not execute code or generate numerical results.**


## Scope and Limitations

* The system supports script generation only, not model execution or inference.

* Analytical capabilities are limited to what is feasible with the variables available in the dictionaries.

* Statistical optimality is not guaranteed; the focus is on correctness, transparency, and reproducibility.

## Reproducibility and Versioning

All system behavior is fully determined by the versioned contents of this repository. For scientific reproducibility, users are encouraged to cite a specific release or commit hash.

## Citation

If you use this system in academic work, please cite the repository and the associated publication (when available).

# PEFES — Engenharia de Prompts para Vigilância Epidemiológica (pt-BR)
**Framework de LLM para Análises Epidemiológicas Reprodutíveis e Validadas por Dicionários de Dados**

## Visão Geral

Este repositório contém a especificação central de um sistema baseado em engenharia de prompts desenvolvido para apoiar análises epidemiológicas utilizando Modelos de Linguagem de Grande Escala (LLMs). O sistema interpreta perguntas em linguagem natural sobre bases brasileiras de vigilância em saúde pública (por exemplo, SINAN e SIVEP) e gera scripts analíticos estritamente fundamentados em dicionários de dados previamente definidos.

O sistema foi projetado para minimizar alucinações, impor rigor metodológico e garantir reprodutibilidade, restringindo explicitamente o modelo a variáveis, bases de dados e escopos analíticos previamente validados.

## Como utilizar

O sistema é baseado em um conjunto de instruções centrais (`pefes_instructions.json`), que definem o comportamento normativo da LLM, incluindo regras de validação, escopo analítico e restrições contra a invenção de variáveis ou dados. Essas instruções constituem o núcleo invariável do sistema.

Além disso, é necessário fornecer ao modelo o dicionário de dados correspondente à base epidemiológica a ser analisada (por exemplo, o dicionário do SINAN Dengue). Esse dicionário descreve formalmente as variáveis disponíveis, seus tipos, domínios e significados, e funciona como a única fonte autorizada de referência para a geração de scripts analíticos.

As instruções centrais e os dicionários de dados **devem ser carregados no contexto da LLM** antes da interação com o usuário. Esse carregamento pode ser realizado por diferentes meios, tais como:

- **Upload direto de arquivos** (JSON ou Markdown) para o contexto do modelo;
- **Injeção de prompt** em mensagens de sistema, contendo o conteúdo das instruções e dos dicionários;
- **Geração aumentada por recuperação (Retrieval-Augmented Generation — RAG)**, na qual um componente externo recupera os arquivos versionados do repositório e fornece os trechos relevantes ao modelo durante a inferência.

Em todos os casos, a LLM **não acessa autonomamente repositórios externos ou a internet**. Todo o conhecimento normativo e estrutural utilizado pelo sistema deve ser explicitamente fornecido no contexto da interação.

## Princípios do Sistema

O sistema opera segundo os seguintes princípios fundamentais:

* **Raciocínio orientado por dicionário**: o LLM só pode referenciar variáveis, categorias e estruturas explicitamente definidas no dicionário de dados ativo.

* **Não invenção de dados**: o sistema nunca assume, simula ou fabrica dados, variáveis ou resultados.

* **Ausência de acesso a dados externos**: todas as informações utilizadas pelo LLM devem ser fornecidas por meio deste repositório ou diretamente pelo usuário.

* **Validação explícita**: todas as solicitações são validadas quanto a escopo, viabilidade e completude antes da geração de qualquer script.

* **Esclarecimento em vez de suposição**: perguntas ambíguas ou incompletas resultam em pedidos de esclarecimento, e não em suposições implícitas.

## Estrutura do Repositório

```
instruction.json                 # Regras e comportamento do sistema principal
/dictionaries
└── *.json                       # Dicionários de dados específicos para cada conjunto de dados
README.md
```


## Fluxo de Funcionamento do Sistema

1. **Interpretação da pergunta**: o sistema analisa a consulta em linguagem natural fornecida pelo usuário.

2. **Validação de contexto**: são verificados a doença, o período, a base de dados e a linguagem de programação.

3. **Planejamento de variáveis**: apenas variáveis presentes no dicionário ativo são selecionadas.

4. **Geração do script**: é gerado um script analítico seguindo boas práticas de análise de dados.

5. **Tratamento de exceções (fallback)**: quando restrições são violadas, o sistema retorna mensagens padronizadas de feedback.

**O sistema não executa código nem gera resultados numéricos.**

## Escopo e Limitações

* O sistema oferece suporte exclusivamente à geração de scripts, não à execução de modelos ou inferência.

* As capacidades analíticas estão limitadas ao que é tecnicamente viável com as variáveis disponíveis nos dicionários de dados.

* Não há garantia de optimalidade estatística; o foco está na correção metodológica, transparência e reprodutibilidade.

## Reprodutibilidade e Versionamento

Todo o comportamento do sistema é inteiramente determinado pelo conteúdo versionado deste repositório.
Para fins de reprodutibilidade científica, recomenda-se citar uma versão específica (release ou hash de commit).

## Citação

Se você utilizar este sistema em trabalhos acadêmicos, por favor cite este repositório e a publicação associada (quando disponível).