# 📋 TRANSFERENCIA_CONHECIMENTO_RAG_DOCLING - VERSÃO 2.0 OTIMIZADA

**Versão:** 2.0 (Auditoria + Consolidação)  
**Data:** 26 de outubro de 2025  
**Baseado em:** Arquivo original + contexto do projeto Obsidian  
**Status:** Pronto para produção

---

## 🎯 RESUMO EXECUTIVO

Este documento consolida toda transferência de conhecimento do projeto RAG Docling em **3 seções críticas**:

1. **Arquitetura & Setup** (o que foi construído)
2. **7 Erros Críticos & Heurísticas** (lições aprendidas)
3. **Checklist de Reconstrução** (como reproduzir)

**Tempo de leitura:** 15 minutos  
**Tempo de implementação:** 4-6 horas (primeira vez)

---

## 📖 SEÇÃO 1: ARQUITETURA COMPLETA

### 1.1 Stack de Tecnologia

```
Windows 11 Pro (i7-11800H, RTX 3060 Laptop, 40GB RAM)
    ↓
WSL2 Ubuntu 22.04 LTS (em D:\wsl_distros\)
    ↓
Docker + Docker Compose
    ↓
Dify Community Edition (http://localhost)
    ↓
Ollama (http://localhost:11434)
    ↓
IBM Granite 4 Micro (granite4:micro, 3B params, 2.1GB)
```

### 1.2 Por Que Cada Escolha

| Componente | Razão |
|------------|-------|
| **WSL2** | Ambiente Linux + acesso GPU NVIDIA via CUDA on WSL |
| **Dify** | Plataforma open-source unificada (UI + RAG + API) |
| **ChromaDB** | Vetor DB leve incluído no Dify |
| **Ollama** | Simplifica deploy de LLMs com API local |
| **Granite4:micro** | 3B params = ótimo balanço VRAM vs performance |

---

## 🚨 SEÇÃO 2: OS 7 ERROS CRÍTICOS

Todos os erros foram **resolvidos** com heurísticas fixas.

### ERRO 01: WSL - Nome Distribuição Inválido

**Problema:** `wsl --export Ubuntu` falha com `WSL_E_DISTRO_NOT_FOUND`

**Causa:** Nome interno ≠ nome de exibição

**Solução:**
```powershell
wsl --list --verbose  # Use nome EXATO desta lista
```

**Heurística #1:** ✅ Sempre consultar `wsl --list --verbose` antes de `--export`, `--unregister`, `--import`

---

### ERRO 02: WSL - Login Como Root Após Import

**Problema:** Após `wsl --import`, entra como `root` ao invés de `diablo`

**Causa:** Import não preserva config de usuário padrão

**Solução:**
```bash
# Dentro do WSL:
sudo nano /etc/wsl.conf

# Adicionar:
[user]
default=diablo

# Salvar: Ctrl+O, Enter, Ctrl+X
```

**Heurística #2:** ✅ Após qualquer `wsl --import`, SEMPRE configurar `/etc/wsl.conf`

---

### ERRO 03: Mudanças em /etc/wsl.conf Não Aplicam

**Problema:** Editar `/etc/wsl.conf` não faz efeito

**Causa:** WSL lê config apenas na inicialização

**Solução:**
```powershell
# PowerShell
wsl --shutdown
# Aguardar 10 segundos
# Reabrir WSL
```

**Heurística #3:** ✅ Após editar `/etc/wsl.conf` OU adicionar usuário a grupo, SEMPRE `wsl --shutdown`

---

### ERRO 04: Docker Daemon Não Está Rodando

**Problema:** `docker compose up -d` falha com `Cannot connect to Docker daemon`

**Causa:** Serviço não inicia automaticamente (systemd desabilitado)

**Solução:**
```bash
# Terminal Ubuntu
sudo service docker start
sudo service docker status  # Verificar
```

**Heurística #4:** ✅ PRIMEIRA COISA após abrir terminal WSL: `sudo service docker start`

---

### ERRO 05: Permission Denied em Docker Socket

**Problema:** `docker compose` falha com `permission denied while trying to connect to the Docker daemon socket`

**Causa:** Usuário adicionado ao grupo mas sessão não recarregou

**Solução:**
```bash
# Adicionar ao grupo:
sudo usermod -aG docker diablo

# Depois (PowerShell):
wsl --shutdown
```

**Heurística #5:** ✅ Após `usermod -aG docker`, SEMPRE `wsl --shutdown` antes de usar docker sem sudo

---

### ERRO 06: Ollama Server Not Responding

**Problema:** `ollama run granite4:micro` falha com `ollama server not responding`

**Causa:** Servidor Ollama não iniciado

**Solução:**
```bash
# Terminal 1 (NÃO FECHAR):
ollama serve

# Terminal 2:
ollama run granite4:micro
```

**Heurística #6:** ✅ Ollama SEMPRE em terminal DEDICADO com `ollama serve` rodando

---

### ERRO 07: Nome do Modelo Inválido

**Problema:** `ollama run ibm-granite/granite-4.0-micro` falha com `pull model manifest: file does not exist`

**Causa:** Nomes diferem entre Hugging Face e Ollama

**Solução:**
```bash
# Correto (Ollama):
ollama pull granite4:micro

# Verificar em: https://ollama.com/library
```

**Heurística #7:** ✅ SEMPRE usar nomes de https://ollama.com/library (não Hugging Face)

---

## ✅ SEÇÃO 3: CHECKLIST DE RECONSTRUÇÃO

### Fase 1: WSL2 + Ubuntu (30 min)

- [ ] PowerShell: `wsl --list --verbose` (verificar)
- [ ] PowerShell: `wsl --install -d Ubuntu` (se não existe)
- [ ] Criar pasta: `D:\wsl_distros\`
- [ ] PowerShell: Export → Unregister → Import para D:\
- [ ] WSL: Editar `/etc/wsl.conf` (user=diablo)
- [ ] PowerShell: `wsl --shutdown`
- [ ] WSL: Verificar `whoami = diablo`

### Fase 2: GPU + Docker (45 min)

- [ ] Windows: Instalar driver NVIDIA (https://nvidia.com/Download)
- [ ] WSL: Testar `nvidia-smi`
- [ ] WSL: `sudo apt update && sudo apt upgrade`
- [ ] WSL: Instalar Docker (repositório oficial Ubuntu)
- [ ] WSL: `sudo usermod -aG docker diablo`
- [ ] PowerShell: `wsl --shutdown`
- [ ] WSL: Testar `docker run hello-world`

### Fase 3: Dify (20 min)

- [ ] WSL: `mkdir -p /mnt/d/projetos/dify && cd $_`
- [ ] WSL: `git clone https://github.com/langgenius/dify.git`
- [ ] WSL: `cd dify/docker && docker compose up -d`
- [ ] Navegador: `http://localhost` (criar conta)

### Fase 4: Ollama (20 min)

- [ ] WSL Terminal 1: `ollama serve` (deixar rodando)
- [ ] WSL Terminal 2: `ollama pull granite4:micro`
- [ ] Dify: Settings → Model Provider → Ollama
- [ ] Dify: Base URL = `http://[IP_WSL]:11434`
- [ ] Dify: Test "Get Models"

### Fase 5: Knowledge Base + Chatbot (15 min)

- [ ] Dify: Create Knowledge Base (adicionar diários)
- [ ] Dify: Create Chatbot com Knowledge Base
- [ ] Dify: Publicar e testar

---

## 🧠 7 HEURÍSTICAS RESUMIDAS

| # | Heurística | Quando Usar |
|---|-----------|------------|
| 1 | `wsl --list --verbose` | Antes de gerenciar WSL |
| 2 | Editar `/etc/wsl.conf` | Após `wsl --import` |
| 3 | `wsl --shutdown` | Após editar config ou adicionar a grupo |
| 4 | `sudo service docker start` | PRIMEIRA coisa no WSL |
| 5 | Adicionar ao grupo → `wsl --shutdown` | Depois de `usermod -aG` |
| 6 | Terminal dedicado: `ollama serve` | Antes de qualquer comando ollama |
| 7 | Usar nomes do `ollama.com/library` | Ao puxar modelos |

---

## 📊 ESTRUTURA DE PASTAS

```
D:\
├── wsl_distros\Ubuntu\          ← Distribuição WSL2
├── Projetos\dify\               ← Dify + Docker
├── Documents\Docling_RAG_Project\ ← Obsidian Vault
└── diario_de_bordo\             ← Este arquivo + logs
```

---

## 🔗 LINKS CRÍTICOS

- GitHub Docling: https://github.com/docling-project/docling
- Dify Docs: https://docs.dify.ai/
- Ollama Library: https://ollama.com/library
- NVIDIA WSL Drivers: https://nvidia.com/Download

---

## ⏰ TEMPOS ESTIMADOS

| Tarefa | Tempo |
|--------|-------|
| Fase 1-5 Completa (primeira vez) | 4-6 horas |
| Setup Diário (after shutdown) | 5 minutos |
| Troubleshoot típico | 10-15 minutos |

---

## ✨ RESULTADO FINAL

Você terá um **sistema de IA 100% local, auto-hospedado, com:**

✅ Interface web (Dify) em `http://localhost`  
✅ LLM rodando localmente (Ollama)  
✅ Base de conhecimento (Docling docs)  
✅ Zero custos recorrentes de API  
✅ Privacidade total

---

## 🚀 PRÓXIMOS PASSOS

1. Executar Checklist Fase 1-5
2. Importar diários do GitHub para Obsidian
3. Criar Base de Conhecimento no Dify
4. Começar com rotina diária de setup
5. Evitar os 7 erros usando heurísticas

---

**STATUS:** ✅ Pronto para Reconstrução  
**ÚLTIMA ATUALIZAÇÃO:** 26/10/2025  
**MANTIDO POR:** Auto

