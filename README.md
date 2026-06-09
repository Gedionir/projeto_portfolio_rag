# projeto_portfolio_rag
Assistente inteligente com RAG (ChromaDB) + Tool-use (Calculator/Lookup Doc) usando Google Gemini. Sistema híbrido para consulta a papers acadêmicos e cálculos matemáticos.

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

## Resultados

| Métrica | Valor |
|---------|-------|
| Faithfulness | 0.75 |
| Context Precision | 1.00 |
| Answer Relevancy | 1.00 |

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
