# 📔 DIÁRIO DOCLING - VERSÃO 2.0 OPERACIONAL

**Versão:** 2.0 (Otimizada para Uso Diário)  
**Data:** 26 de outubro de 2025  
**Status:** Pronto para Reconstrução Pós-Formatação  
**Ambiente:** Windows 11 + WSL2 Ubuntu 22.04 + Docker + Dify + Ollama

---

## 🎯 RESUMO EM 30 SEGUNDOS

Sistema de IA especialista local para Docling. Setup:
- **3 modelos LLM** (Granite, Qwen 4K, Qwen 32K)
- **1 modelo Embedding** (Nomic)
- **Interface RAG** via Dify
- **GPU RTX 3060** para inferência local
- **Zero APIs pagas**

---

## 📊 RÁPIDA REFERÊNCIA - USAR ESTE MODELO

| Caso de Uso | Modelo | Tempo |
|------------|--------|-------|
| Resposta rápida | Granite 4.0 Micro | 1-3s ⚡ |
| Código/Documentação | Qwen 4K | 2-5s ⚙️ |
| Documentos longos | Qwen 32K | 3-7s 📚 |
| RAG/Busca | Nomic Embed | 10-50ms 🔍 |

---

## 🔧 QUICK SETUP - 4 FASES

### FASE 1: Ambiente Base (30 min)
```bash
# WSL2
wsl --list --verbose
wsl --import Ubuntu D:\wsl_distros\Ubuntu [backup-file]
# Editar: /etc/wsl.conf → [user] default=diablo
wsl --shutdown

# Docker
sudo service docker start
sudo usermod -aG docker diablo
wsl --shutdown
```

### FASE 2: Dify + Docker (20 min)
```bash
cd /mnt/d/projetos/dify
git clone https://github.com/langgenius/dify.git
cd dify/docker
docker compose up -d
# Acesso: http://localhost
```

### FASE 3: Ollama + Modelos (30 min)
```bash
# Terminal 1 (DEDICADO):
ollama serve

# Terminal 2:
ollama pull granite4:micro        # 2.1GB
ollama pull qwen2.5-coder:14b     # 9GB
ollama pull qwen2.5-coder:14b-32k # 9GB (compartilha layers)
ollama pull nomic-embed-text      # 274MB
```

### FASE 4: Dify + Modelos (10 min)
```
Dify Web → Settings → Model Provider → Ollama
Base URL: http://[IP_WSL]:11434
Get Models: Deve listar 4 modelos ✅
```

---

## 🧠 ESPECIFICAÇÕES DOS MODELOS

### 1. IBM Granite 4.0 Micro ⚡
- **Tamanho:** 3.4B params, 2.1GB disco
- **VRAM:** 2-3GB
- **Velocidade:** 80-100 tok/s
- **Uso:** Respostas rápidas, perguntas básicas
- **Latência:** 1-3s

### 2. Qwen2.5-Coder 14B (4K) ⚙️
- **Tamanho:** 14B params, 9GB disco
- **VRAM:** 4.2GB
- **Velocidade:** 30-50 tok/s
- **Uso:** Código, docs técnicas, 4K contexto
- **Latência:** 2-5s

### 3. Qwen2.5-Coder 14B-32K (32K) 📚
- **Tamanho:** 14B params, 9GB disco (compartilha)
- **VRAM:** 5-5.5GB
- **Velocidade:** 25-40 tok/s
- **Uso:** Documentos longos, 32K contexto
- **Latência:** 3-7s

### 4. Nomic Embed Text 🔍
- **Tamanho:** 137M params, 274MB disco
- **VRAM:** 300-500MB
- **Velocidade:** 500-1000 embeddings/s
- **Uso:** RAG, busca semântica, indexação
- **Latência:** 10-50ms

---

## 🛠 HARDWARE DISPONÍVEL

```
💻 CPU: Intel i7-11800H (11th Gen)
🎮 GPU: NVIDIA RTX 3060 Laptop (6GB VRAM)
🧠 RAM: 40GB DDR4
💾 Disco: D:\ (workspace)
```

**Restrição Crítica:** 6GB VRAM GPU
- Granite + Nomic = 2.5GB + 0.5MB = ✅ Fits
- Granite + Qwen4K = 2.5GB + 4.2GB = ❌ Tight (13% spillover)
- Qwen4K + Nomic = 4.2GB + 0.5GB = ✅ OK
- Qwen32K = 5.5GB + 0.5GB swap = ⚠️ Use sparingly

---

## 📋 HEURÍSTICAS CRÍTICAS

| # | Regra | Quando |
|---|-------|--------|
| 1 | `wsl --list --verbose` | Antes de qualquer comando wsl |
| 2 | Editar `/etc/wsl.conf` | Após `wsl --import` |
| 3 | `wsl --shutdown` | Após config/grupo/perms |
| 4 | `sudo service docker start` | PRIMEIRA coisa no WSL |
| 5 | Adicionar grupo → shutdown | Depois de `usermod -aG` |
| 6 | `ollama serve` em terminal dedicado | Sempre rodando |
| 7 | Nomes de `ollama.com/library` | Ao puxar modelos |

---

## 🚀 FLUXO DE DECISÃO - QUAL MODELO USAR

```
Precisa resposta agora?
├─ SIM → Granite (2-3s)
└─ NÃO ↓

É sobre código/docs técnicas?
├─ NÃO → Granite
└─ SIM ↓

Doc é < 3000 palavras?
├─ SIM → Qwen 4K (2-5s)
└─ NÃO → Qwen 32K (3-7s)

+ Sempre usar Nomic para busca RAG
```

---

## 📁 ESTRUTURA DE PASTAS

```
D:\
├── wsl_distros\Ubuntu\          ← WSL2
├── Projetos\
│   ├── dify\                    ← Docker Compose
│   └── docling\                 ← Repo Docling
├── Documents\
│   └── Docling_RAG_Project\     ← Obsidian Vault
└── diario_de_bordo\             ← Este arquivo
```

---

## 🔗 LINKS CRÍTICOS

- **Docling:** https://github.com/docling-project/docling
- **Ollama:** https://ollama.com/library
- **Dify:** https://docs.dify.ai/
- **Granite:** https://www.ibm.com/granite/
- **Qwen:** https://github.com/QwenLM/Qwen

---

## 📍 ENDEREÇOS OPERACIONAIS

- **Dify Web:** `http://localhost`
- **Ollama API:** `http://localhost:11434`
- **WSL2 Ubuntu:** Usuario `diablo`
- **Modelos Ollama:**
  - `granite4:micro`
  - `qwen2.5-coder:14b`
  - `qwen2.5-coder:14b-32k`
  - `nomic-embed-text`

---

## ⏰ TEMPOS

| Tarefa | Tempo |
|--------|-------|
| Setup Completo (primeira vez) | 4-6h |
| Setup Diário (após shutdown) | 5 min |
| Troubleshoot típico | 10-15 min |
| Resposta Granite | 1-3s |
| Resposta Qwen 4K | 2-5s |
| Resposta Qwen 32K | 3-7s |

---

## ✨ RESULTADO

✅ IA especialista local 100%  
✅ 3 LLMs + 1 Embedding  
✅ Interface web (Dify)  
✅ Zero APIs pagas  
✅ Privacidade total  
✅ GPU acelerado  

---

## 🔄 MANUTENÇÃO DIÁRIA

1. Abrir WSL terminal
2. `sudo service docker start`
3. Abrir novo terminal: `ollama serve`
4. Abrir novo terminal: trabalhar normalmente
5. Se fechar: repetir desde passo 1

---

## 📝 REGISTRO DE MUDANÇAS

**v2.0 (26/10/2025):**
- Removido: 60% de conteúdo redundante
- Adicionado: Fluxo de decisão modelos
- Otimizado: Tabelas de referência rápida
- Mantido: Todos os 7 erros + heurísticas

---

**STATUS:** ✅ Pronto para Produção  
**ÚLTIMA ATUALIZAÇÃO:** 26/10/2025 01:15  
**MANTIDO POR:** Auto (Obsidian + Diário)

