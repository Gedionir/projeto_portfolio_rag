# Assistente inteligente com RAG (ChromaDB) + Tool-use (Calculator/Lookup Doc) usando Google Gemini. Sistema híbrido para consulta a papers acadêmicos e cálculos matemáticos.

# Assistente Inteligente RAG + Tool-Use

Sistema híbrido que combina **Recuperação Aumentada por Geração (RAG)** com **ferramentas integradas** (calculadora e consulta a documentação), desenvolvido como projeto final da disciplina **Desenvolvimento com IA Generativa**.

## O que este projeto faz

- **RAG:** Busca semântica em 3 papers acadêmicos (Attention Is All You Need, RAG, Lost in the Middle) usando ChromaDB + Gemini Embeddings
- **Tool-use:** Calculator seguro para expressões matemáticas + Lookup Doc para consulta a documentação técnica
- **Roteamento inteligente:** Decide automaticamente entre RAG ou tools baseado na query do usuário

## Tecnologias

| Componente | Tecnologia |
|------------|-----------|
| LLM | Google Gemini 2.5 Flash-Lite |
| Embeddings | Gemini Embedding 001 |
| Vector Store | ChromaDB |
| Chunking | RecursiveCharacterSplitter (800/100) |
| Interface | Google Colab |

## Problema

**Domínio:** Assistente acadêmico para consulta a papers de Inteligência Artificial e Machine Learning.

**Persona-alvo:** Estudantes e pesquisadores que precisam consultar rapidamente conceitos técnicos de papers clássicos (Attention Is All You Need, RAG, Lost in the Middle) sem ler o documento completo.

**Por que LLM + RAG?**
- O corpus contém informações técnicas densas que exigem recuperação precisa
- O LLM sozinho alucinaria sobre detalhes específicos dos papers
- O tool-use (calculadora) é necessário para cálculos auxiliares durante a leitura

**3 Perguntas representativas que o sistema responde:**

| Pergunta | Tipo | Resposta Esperada |
|----------|------|-------------------|
| "Qual a diferença entre fine-tuning e RAG?" | RAG | Fine-tuning ajusta pesos; RAG injeta contexto |
| "Calcule 25% de 480" | Calculator | 120.0 |
| "O que é chunking?" | RAG | Processo de dividir documentos em pedaços menores para indexação |


## Arquitetura

O usuário digita uma pergunta no Google Colab. Um roteador analisa a query: se contém números e operadores, chama a Calculator Tool; se começa com "doc:", chama o Lookup Doc Tool; caso contrário, aciona o pipeline RAG, que busca no ChromaDB e gera resposta com o Gemini. Por fim, o resultado é exibido ao usuário.

┌─────────────────────────────────────────────────────────────┐
│                         USUÁRIO                             │
│                   digita uma pergunta                       │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                   ROTEADOR INTELIGENTE                      │
│              decide qual caminho usar                       │
└─────────┬───────────────┼───────────────┬───────────────────┘
          │               │               │
          ▼               ▼               ▼
┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│  TEM NÚMERO │   │  COMEÇA COM │   │  PERGUNTA   │
│  E OPERADOR │   │   "doc:"    │   │   NORMAL    │
└──────┬──────┘   └──────┬──────┘   └──────┬──────┘
       │                 │                 │
       ▼                 ▼                 ▼
┌─────────────┐   ┌─────────────┐   ┌─────────────────────────┐
│ CALCULATOR  │   │ LOOKUP DOC  │   │        PIPELINE RAG     │
│  Tool       │   │   Tool      │   │                         │
│             │   │             │   │  ┌───────────────────┐  │
│ 3 + 5 = 8   │   │ doc: retry  │   │  │   ChromaDB        │  │
│             │   │             │   │  │  (busca semântica)│  │
└──────┬──────┘   └──────┬──────┘   │  └─────────┬─────────┘  │
       │                 │          │            │            │
       │                 │          │            ▼            │
       │                 │          │  ┌───────────────────┐  │
       │                 │          │  │  Gemini LLM       │  │
       │                 │          │  │  (gera resposta)  │  │
       │                 │          │  └─────────┬─────────┘  │
       │                 │          └────────────┼────────────┘
       │                 │                       │
       └─────────────────┼───────────────────────┘
                         │
                         ▼
          ┌─────────────────────────────┐
          │         RESPOSTA            │
          │  + fonte (se foi RAG)       │
          │  + resultado (se foi tool)  │
          └─────────────────────────────┘


## Decisões de Design

**1. Chunk 800/overlap 100** – Papers têm frases longas. 800 chars capturam o parágrafo completo; overlap 100 evita corte de informação na fronteira.

**2. Roteamento por regras** – Em vez de classificador ML, usei regras simples (números/operadores ou "doc:"). Zero custo de inferência, latência insignificante.

**3. Gemini Embedding** – Priorizei qualidade (context_precision=1.00) mesmo com rate limit de 15 RPM. Para alto volume, trocaria por sentence-transformers.

**4. Prompt com âncora** – "Responda APENAS com base no contexto" previne alucinações e garante respostas fiéis ao corpus.


## Resultados

| Métrica | Valor |
|---------|-------|
| Faithfulness | 0.75 |
| Context Precision | 1.00 |
| Answer Relevancy | 1.00 |


## Custo

| Métrica | Valor |
|---------|-------|
| **Provider** | Google Gemini Free Tier |
| **Quota diária** | 1.000 requisições/dia |
| **Rate limit** | 15 requisições/minuto |
| **Custo por requisição** | 18% da quota / 1k queries |
| **Custo USD** | $0.00 (Free Tier) |

> **Nota:** Para medir custo real em USD, seria necessário utilizar OpenAI ou Anthropic. Este projeto manteve-se no Gemini Free Tier onde o "custo" é medido por quota consumida.



## Como executar

1. Abra o notebook no Google Colab
2. Configure sua chave `GEMINI_API_KEY`
3. Execute todas as células em ordem
4. Use comandos como:
   - `Qual a diferença entre fine-tuning e RAG?` → RAG
   - `25*4+10` → Calculator
   - `doc: pydantic` → Lookup Doc

## Limitações

- Rate limit: 15 RPM (Gemini Free)
- Corpus fixo: apenas 3 documentos
- Calculator: sem funções avançadas (sqrt, pow)

## Licença

Uso educacional - MIT

---

**Autor:** Gedionir Amaral Paim  
**Disciplina:** Desenvolvimento com IA Generativa  
**Data:** Junho/2026
