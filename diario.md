
Atualizado 20/10/2025
Junção dos diários até 20/10/2025

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Diário de Bordo — Projeto IA Docling

## 1. Identificação e Objetivo

- **ID do Projeto:** `localragdoclingexpert01`
- **Data de Criação:** 2025-10-11T22:22:00-03:00
- **Solicitante:** Usuário individual, desenvolvedor
- **Objetivo:** Construir um sistema de IA especialista, auto-hospedado, para consultas e automação no projeto open-source Docling ([github.com/docling-project/docling](https://github.com/docling-project/docling)).
- **Caso de Uso:** Consultas técnicas, validação de comandos, automação de ingestão de conhecimento, superando limitações de SaaS e APIs pagas.


## 2. Ambiente Técnico

- **Hardware:**
    - Dell G15 (Notebook)
    - CPU: Intel i7-11800H (11th Gen)
    - RAM: 40GB DDR4
    - GPU: NVIDIA GeForce RTX 3060 Laptop (6GB VRAM)
- **Software:**
    - Windows 11 Pro x64
    - WSL2 Ubuntu 24.04 (instalado em D:\, usuário padrão: `diablo`)
    - Docker \& Docker Compose
    - Dify Community Edition (1.9.1)
    - Ollama (0.12.6)
    - ChromaDB
    - Docling instalado
- **Assinaturas/Tokens:**
    - Dify Pro (educacional)
    - Google One Pro
    - Google AI Pro2
- **Políticas:**
    - Solução 100% local, sem dependência de APIs pagas
    - Privacidade e controle total dos dados
    - Uso da GPU local para inferência


## 3. Modelos de IA Instalados — Auditoria Completa

### 3.1. IBM Granite 4.0 Micro

- **Nome:** IBM Granite 4.0 Micro
- **Desenvolvedor:** IBM Research
- **Arquitetura:** Transformer híbrido Mamba
- **Parâmetros:** 3.4 bilhões (3.4B)
- **Tamanho em Disco:** 2.1 GB (Q4 quantizado)
- **Context Window:** 4096 tokens (4K)
- **Vocabulário:** 49.152 tokens
- **Precision:** Q4_0 (4-bit)
- **Uso de Recursos:**
    - VRAM: 2-3 GB
    - RAM: ~1 GB
    - Distribuição: 100% GPU
- **Performance:**
    - Velocidade: 80-100 tokens/segundo
    - Latência: 1-3 segundos
    - First Token: ~300-500ms
- **Especialização:**
    - Propósito geral, raciocínio básico, multilingue
    - Bom para perguntas rápidas, listas, instruções básicas
    - Limitado para análise técnica profunda ou código complexo


### 3.2. Qwen2.5-Coder:14B (4K)

- **Nome:** Qwen2.5-Coder:14B
- **Desenvolvedor:** Alibaba Cloud (Qwen Team)
- **Arquitetura:** Transformer (decoder-only)
- **Parâmetros:** 14 bilhões (14B)
- **Tamanho em Disco:** 9.0 GB (Q4_K_M)
- **Context Window:** 4096 tokens (4K)
- **Vocabulário:** 152.064 tokens
- **Camadas:** 65 (49 principais + 16 auxiliares)
- **Precision:** Q4_K_M
- **Uso de Recursos:**
    - VRAM: ~4.2 GB (19 camadas)
    - RAM: ~9 GB (30 camadas)
    - Distribuição: GPU + CPU (híbrido)
- **Performance:**
    - Velocidade: 30-50 tokens/segundo
    - Latência: 2-5 segundos
    - First Token: ~800ms-1.2s
- **Especialização:**
    - Código e programação (116 linguagens)
    - Documentação técnica, análise de código, tutoriais
    - Multilingue, raciocínio técnico avançado
    - Ideal para explicações técnicas, análise de código Docling, debugging


### 3.3. Qwen2.5-Coder:14B-32K (32K)

- **Nome:** Qwen2.5-Coder:14B-32K
- **Base:** Qwen2.5-Coder:14B
- **Parâmetros:** 14 bilhões (14B)
- **Tamanho em Disco:** 9.0 GB (compartilha layers)
- **Context Window:** 32.768 tokens (32K)
- **Max Output Tokens:** 8192
- **Vocabulário:** 152.064 tokens
- **Camadas:** 65
- **Precision:** Q4_K_M
- **Uso de Recursos:**
    - VRAM: ~5-5.5 GB
    - RAM: ~11-13 GB
    - Cache KV: ~768 MB (GPU) + ~1.2 GB (CPU)
    - Distribuição: GPU + CPU
- **Performance:**
    - Velocidade: 25-40 tokens/segundo
    - Latência: 3-7 segundos
    - First Token: ~1-2s
- **Especialização:**
    - Contexto 8x maior (32K)
    - Documentos longos, análise de codebases, histórico extenso
    - Sumarização de documentos grandes
    - Ideal para análise de PDFs, code review de projetos grandes, troubleshooting longo


### 3.4. Nomic-Embed-Text

- **Nome:** Nomic Embed Text v1.5
- **Desenvolvedor:** Nomic AI
- **Tipo:** Embedding Model (não é LLM)
- **Arquitetura:** BERT-based encoder
- **Parâmetros:** 137 milhões (137M)
- **Tamanho em Disco:** 274 MB
- **Dimensão dos Embeddings:** 768
- **Context Window:** 8192 tokens (8K)
- **Precision:** Float16
- **Uso de Recursos:**
    - VRAM: ~300-500 MB
    - RAM: ~500 MB
    - Distribuição: 100% GPU
- **Performance:**
    - Velocidade: ~500-1000 embeddings/segundo
    - Latência: ~10-50ms por texto
    - Suporta batches grandes
- **Especialização:**
    - Busca semântica, RAG, similaridade de documentos, clustering, classificação
    - Essencial para indexação e busca inteligente na base de conhecimento Docling


## 4. Tabela Comparativa dos Modelos

| Característica | Granite 4.0 Micro | Qwen2.5-Coder:14B (4K) | Qwen2.5-Coder:14B-32K | Nomic Embed Text |
| :-- | :-- | :-- | :-- | :-- |
| Parâmetros | 3.4B | 14B | 14B | 137M |
| Tamanho em Disco | 2.1 GB | 9.0 GB | 9.0 GB | 274 MB |
| Context Window | 4K | 4K | 32K | 8K |
| VRAM | 2-3 GB | 4.2 GB | 5-5.5 GB | 0.3-0.5 GB |
| RAM | 1 GB | 9 GB | 11-13 GB | 0.5 GB |
| Tokens/seg | 80-100 | 30-50 | 25-40 | N/A |
| Latência | 1-3s | 2-5s | 3-7s | 10-50ms |
| GPU % | 100% | 39% | 35-40% | 100% |
| CPU % | 0% | 61% | 60-65% | 0% |
| Qualidade | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | N/A |
| Código | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | N/A |
| Docs Longos | ❌ | ⚠️ | ✅✅✅ | ✅ |
| Velocidade | ✅✅✅ | ✅✅ | ✅ | ✅✅✅ |

## 5. Estratégia de Uso — Guia Prático

### Fluxograma de Decisão

- **Precisa de resposta rápida?**
    - SIM → Granite 4.0 Micro
    - NÃO → É sobre código/documentação técnica?
        - NÃO → Granite 4.0 Micro
        - SIM → O texto/doc é maior que 3000 palavras?
            - NÃO → Qwen2.5-Coder:14B (4K)
            - SIM → Qwen2.5-Coder:14B-32K
- **Precisa buscar em base de docs?**
    - SIM → Nomic-Embed-Text + LLM para resposta


### Casos de Uso

- **Aprendizado Inicial:** Granite 4.0 Micro
- **Implementação Técnica:** Qwen2.5-Coder:14B (4K)
- **Análise de Documentação Completa:** Qwen2.5-Coder:14B-32K
- **Base de Conhecimento (RAG):** Nomic-Embed-Text + Qwen2.5-Coder


## 6. Configurações no Dify

- **Granite 4.0 Micro**
    - Model Name: ibm/granite4:micro
    - Model Type: LLM
    - Base URL: http://ollama:11434
    - Context Size: 4096
    - Max Tokens: 2048
    - Temperature: 0.7 (criativo) ou 0.3 (preciso)
- **Qwen2.5-Coder:14B**
    - Model Name: qwen2.5-coder:14b
    - Model Type: LLM
    - Base URL: http://ollama:11434
    - Context Size: 4096
    - Max Tokens: 4096
    - Temperature: 0.5 (código)
- **Qwen2.5-Coder:14B-32K**
    - Model Name: qwen2.5-coder:14b-32k
    - Model Type: LLM
    - Base URL: http://ollama:11434
    - Context Size: 32768
    - Max Tokens: 8192
    - Temperature: 0.5 (código) ou 0.7 (docs)
- **Nomic-Embed-Text**
    - Model Name: nomic-embed-text
    - Model Type: Text Embedding
    - Base URL: http://ollama:11434
    - Embedding dimensions: 768
    - Max input tokens: 8192


## 7. Estado Atual do Projeto

- **Fase:** Transição entre Fase 2 e início da Fase 3
- **Componentes Implantados:**

1. WSL2 instalado e configurado
2. Ubuntu movido para D:\, usuário ‘diablo’ padrão
3. Drivers NVIDIA para WSL2 instalados, acesso confirmado por `nvidia-smi`
4. Docker instalado/configurado
5. Dify e ChromaDB rodando via `docker compose`, interface web ok
6. Ollama instalado e funcional
7. Modelos Granite, Qwen2.5-Coder (4K e 32K), Nomic-Embed baixados e testados


## 8. Registro de Aprendizados \& Heurísticas

### 8.1. WSL2

- **Erro01:** `wsl --export nome` falhou (WSLEDISTRONOTFOUND). Nome interno ≠ nome de exibição. Sempre consultar `wsl --list --verbose`.
- **Erro02:** Após `wsl --import`, login padrão era root. Editar/criar `/etc/wsl.conf` com `user.default=username`, reiniciar WSL via `wsl --shutdown`.


### 8.2. Docker no WSL2

- **Erro04:** `docker compose up -d` falha (Cannot connect to Docker daemon). Após boot/reboot/`wsl --shutdown`, executar `sudo service docker start`.
- **Erro05:** Permissão negada ao conectar daemon. Após `usermod -aG docker user`, fechar todas sessões e rodar `wsl --shutdown`.


### 8.3. Ollama no WSL2

- **Erro06:** `ollama run` não responde (server not running). Iniciar manual com `ollama serve`.
- **Erro07:** `ollama run huggingface` falha (manifest file does not exist). Conferir nome oficial na [biblioteca Ollama](https://ollama.com/library).


## 9. Roadmap: Próximos Passos \& Automação

- **Fase 3, Passo 4:**

1. Obter IP interno do WSL2 (`hostname -I`)
2. Configurar “provedor Ollama” na interface web do Dify
3. Adicionar modelos Granite, Qwen2.5-Coder, Nomic-Embed
- **Fase 4:** Criar pasta Ddebordo no Windows, arquivo Markdown, ingestão via Dify
- **Fase 5:** Testes de assistente de IA, base de conhecimento conectada, validação de inferência
- **Roadmap Futuro:**
    - Pipeline de ingestão automática para Docling (Python + LlamaIndex)
    - Dogfooding com docling e granite-docling-258M
    - Integração do pipeline à API do Dify
    - Monitor de commits via Google Apps Script + API GitHub


## 10. Endereços e Dados Técnicos

- **Repositório Docling:** [https://github.com/docling-project/docling](https://github.com/docling-project/docling)
- **Dify Web Local:** `http://localhost`
- **WSL2 IP Interno:** consulte via comando `hostname -I`
- **Modelos Ollama:**
    - `granite4micro`
    - `qwen2.5-coder:14b`
    - `qwen2.5-coder:14b-32k`
    - `nomic-embed-text`
- **Usuário Ubuntu WSL2:** `diablo`
- **Pasta Diário de Bordo Windows:** `D:\Ddebordo`
- **Base de Conhecimento Dify:** (adicionar caminho/link virtual usado)


## 11. Boas Práticas e Recomendações

- Documentar novas falhas, soluções e heurísticas
- Preferir scripts e automação via Python para pipelines
- Seguir atualização via dogfooding e automações de commit
- Reforçar testes de integração entre Ollama, Dify e o pipeline
- Evitar dependência de APIs externas e SaaS fora contexto educacional/teste
- Priorizar compatibilidade dos comandos com Windows 11 e WSL2

***

**FIM DO DIÁRIO — Estrutura pronta para parsing por LLM e automação incremental.**
<span style="display:none">[^1][^10][^11][^12][^13][^14][^15][^16][^17][^18][^19][^2][^20][^21][^22][^23][^24][^25][^26][^27][^28][^29][^3][^30][^31][^32][^33][^34][^35][^36][^37][^4][^5][^6][^7][^8][^9]</span>

<div align="center">⁂</div>

[^1]: Explicacao.pdf

[^2]: https://github.com/ollama/ollama/issues/10557

[^3]: https://github.com/ollama/ollama/issues/7629

[^4]: https://github.com/ollama/ollama/issues/8349

[^5]: https://github.com/ollama/ollama/issues/10752

[^6]: https://github.com/ollama/ollama/issues/7711

[^7]: https://github.com/ollama/ollama/issues/3369

[^8]: https://github.com/ollama/ollama/issues/4916

[^9]: https://github.com/ollama/ollama/issues/9639

[^10]: https://github.com/ollama/ollama/issues/4912

[^11]: https://github.com/ollama/ollama/issues/4964

[^12]: https://github.com/ollama/ollama/issues/10612

[^13]: https://github.com/ollama/ollama/issues/9416

[^14]: https://github.com/ollama/ollama/issues/10823

[^15]: https://github.com/ollama/ollama/issues/3372

[^16]: https://github.com/ollama/ollama/issues/7673

[^17]: https://github.com/ollama/ollama/issues/4457

[^18]: https://ollama.com/ibm/granite4:micro-q2_K

[^19]: https://diolinux.com.br/noticias/ibm-lanca-granite-4-0.html

[^20]: https://www.reddit.com/r/LocalLLaMA/comments/1nw2wd6/granite_40_language_models_a_ibmgranite_collection/

[^21]: https://www.ibm.com/granite/docs/run/granite-with-ollama-windows

[^22]: https://www.ibm.com/new/announcements/ibm-granite-4-0-hyper-efficient-high-performance-hybrid-models

[^23]: https://www.byteplus.com/en/topic/417609

[^24]: https://collabnix.com/ollama-embedded-models-the-complete-technical-guide-to-local-ai-embeddings-in-2025/

[^25]: https://www.ibm.com/granite/docs/run/granite-with-ollama-mac

[^26]: https://www.datacamp.com/tutorial/qwen-coder-2-5

[^27]: https://collabnix.com/ollama-embedded-models-the-complete-technical-guide-for-2025-enterprise-deployment/

[^28]: https://ollama.com/ibm/granite4/tags

[^29]: https://ollama.com/library/qwen2.5-coder

[^30]: https://ollama.com/library/nomic-embed-text

[^31]: https://ollama.com/ibm/granite4.0-preview:tiny-base-q3_K_M

[^32]: https://www.reddit.com/r/LocalLLaMA/comments/1gxs34g/comment_your_qwen_coder_25_setup_ts_here/

[^33]: https://ollama.com/toshk0/nomic-embed-text-v2-moe

[^34]: https://ollama.com/blog/ibm-granite

[^35]: https://ollama.com/MHKetbi/Qwen2.5-Coder-32B-Instruct

[^36]: https://www.nomic.ai/blog/posts/nomic-embed-text-v1

[^37]: https://www.byteplus.com/en/topic/417612

