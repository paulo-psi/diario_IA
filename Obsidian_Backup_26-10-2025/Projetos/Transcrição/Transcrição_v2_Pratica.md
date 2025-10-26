# 🎬 DIÁRIO TRANSCRIÇÃO VIDEO - VERSÃO 2.0 PRÁTICA

**Versão:** 2.0 (Otimizada para Execução)  
**Data:** 26 de outubro de 2025  
**Status:** Pronto para Setup  
**Ambiente:** Windows 11 + WSL2 Ubuntu 22.04 + Faster Whisper + GPU RTX 3060

---

## 🎯 EM 30 SEGUNDOS

Download + transcrição automática de vídeos HLS protegidos + legendas em PT-BR, acelerado com GPU NVIDIA.

**Setup:** 1.5 horas  
**Transcrição por hora de vídeo:** 5-10 minutos (GPU)

---

## 🔧 SETUP RÁPIDO - 5 PASSOS

### PASSO 1: Preparar Pastas & Ferramentas (5 min)

```bash
# Terminal Ubuntu WSL2
mkdir -p ~/videos ~/faster_whisper_project
cd ~/videos

sudo apt update
sudo apt install -y yt-dlp wget ffmpeg python3-pip
```

### PASSO 2: Instalar Python + Dependências (10 min)

```bash
cd ~/faster_whisper_project

# Criar virtual env
python3 -m venv venv
source venv/bin/activate

# Instalar dependências
pip install --upgrade pip
pip install faster-whisper torch torchvision torchaudio
```

### PASSO 3: Instalar CUDA 12.1 + cuDNN 9 (30 min)

**Problema:** Ubuntu 22.04 default = cuDNN 8, mas Faster Whisper precisa de cuDNN 9 para GPU

**Solução:** Script automatizado

```bash
#!/bin/bash
set -e

echo "📥 Configurando CUDA 12.1 + cuDNN 9..."

# 1. Adicionar repositório NVIDIA
curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/3bf863cc.pub | sudo gpg --dearmor -o /usr/share/keyrings/cuda-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/cuda-archive-keyring.gpg] https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/ /" | sudo tee /etc/apt/sources.list.d/cuda.list

# 2. Instalar CUDA + cuDNN
sudo apt update
sudo apt install -y cuda-toolkit-12-1 libcudnn9 libcudnn9-dev

# 3. Configurar PATH
echo 'export PATH=/usr/local/cuda-12.1/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-12.1/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc

# 4. Criar symlinks (Compatibilidade)
sudo ln -sf /usr/local/cuda-12.1/lib64/libcudnn_ops_infer.so.9 /usr/local/cuda-12.1/lib64/libcudnn_ops.so.9
sudo ln -sf /usr/local/cuda-12.1/lib64/libcudnn_cnn_infer.so.9 /usr/local/cuda-12.1/lib64/libcudnn_cnn.so.9
sudo ln -sf /usr/local/cuda-12.1/lib64/libcudnn.so.9 /usr/local/cuda-12.1/lib64/libcudnn.so.9

# 5. Reiniciar WSL
powershell.exe wsl --shutdown

echo "✅ CUDA + cuDNN prontos! Reabrua Ubuntu"
```

**Salve como:** `setup_cuda.sh` → `chmod +x setup_cuda.sh` → `./setup_cuda.sh`

### PASSO 4: Verificar GPU (2 min)

```bash
# Testar CUDA
nvidia-smi  # Deve mostrar RTX 3060 + CUDA 12.1

# Testar PyTorch
python3 -c "import torch; print(torch.cuda.is_available()); print(torch.cuda.get_device_name(0))"
# Esperado: True + GeForce RTX 3060
```

### PASSO 5: Preparar Links de Vídeos (10 min)

**No Windows:**

1. Firefox + extensões:
   - `cookies.txt` (salvar cookies autenticados)
   - `HLS Stream Detector` (extrair URLs)

2. Acessar URL protegida
3. Exportar cookies.txt → salvar em `~/videos/`
4. Detectar streams HLS + URLs legendas

**No WSL:**

```bash
cd ~/videos

# Listar vídeos 1080p
grep -o 'https://[^ ]*playlist-1080p\.m3u8[^ ]*' links.txt > lista_videos.txt

# Listar legendas PT-BR
grep -o 'https://[^ ]*pt-br\.srt[^ ]*' links.txt > lista_legendas.txt
```

---

## 🚀 FLUXO DE DOWNLOAD + TRANSCRIÇÃO

### Download com yt-dlp

```bash
cd ~/videos

# Baixar vídeos com cookies + referer (evita bloqueios)
yt-dlp -a lista_videos.txt \
       --cookies cookies.txt \
       --referer "https://[seu-dominio]/" \
       -o "aula_%(autonumber)02d.mp4" \
       --concurrent-fragments 4

# Baixar legendas
wget -O aula_01.srt "https://[legenda-url-1]"
wget -O aula_02.srt "https://[legenda-url-2]"
# ... repetir para cada legenda
```

### Transcrição Batch com Faster Whisper

**Salve como:** `transcribe_batch.py`

```python
#!/usr/bin/env python3
import os, gc, torch
from faster_whisper import WhisperModel

# GPU = float16 (rápido), CPU = int8 (lento mas funciona)
device = "cuda"  # ou "cpu"
compute_type = "float16"  # ou "int8"

print(f"🎬 Usando device: {device} | compute_type: {compute_type}")

model = WhisperModel("medium", device=device, compute_type=compute_type)
video_folder = os.path.expanduser("~/videos")

for f in sorted(os.listdir(video_folder)):
    if f.lower().endswith(".mp4"):
        mp4_path = os.path.join(video_folder, f)
        transcr_file = f.replace(".mp4", "_transcricao.txt")
        transcr_path = os.path.join(video_folder, transcr_file)
        
        if not os.path.exists(transcr_path):
            print(f"\n🎬 Transcrevendo: {f}")
            
            # Transcrever
            segments, info = model.transcribe(
                mp4_path,
                language="pt",
                beam_size=1,
                vad_filter=True,
                vad_parameters=dict(min_silence_duration_ms=500)
            )
            
            # Salvar
            with open(transcr_path, "w", encoding="utf-8") as fo:
                for seg in segments:
                    fo.write(f"[{seg.start:.2f}s→{seg.end:.2f}s] {seg.text}\n")
            
            print(f"✅ Salvo: {transcr_file}")
            
            # Limpar GPU
            gc.collect()
            torch.cuda.empty_cache()
        else:
            print(f"⏭️  Já existe: {transcr_file}")

print("\n🎉 Todas as transcrições concluídas!")
```

**Executar:**

```bash
cd ~/faster_whisper_project
source venv/bin/activate
python3 transcribe_batch.py

# Monitorar GPU em outro terminal:
watch -n 1 nvidia-smi
```

---

## 📊 DESEMPENHO ESPERADO

| Duração Vídeo | Tempo Transcrição (GPU) | Modelo |
|---------------|------------------------|--------|
| 1 hora | 5-10 min | Granite ou Whisper Medium |
| 10 horas | 50-100 min | RTX 3060 + 6GB VRAM |
| Limitação | VRAM 6GB = ~30min contínuo | Sem crash |

---

## 🛠 TROUBLESHOOTING

### Erro: "CUDA não disponível"

```bash
# Verificar instalação
nvcc --version  # Deve mostrar CUDA 12.1
ldconfig -p | grep cudnn  # Deve mostrar libcudnn.so.9
```

**Solução:** Rodar `setup_cuda.sh` novamente + `wsl --shutdown`

### Erro: "libcudnn_ops_infer.so.9 não encontrado"

```bash
# Verificar se symlinks existem
ls -la /usr/local/cuda-12.1/lib64/libcudnn*

# Se faltar, criar manualmente:
sudo ln -sf /usr/local/cuda-12.1/lib64/libcudnn_ops_infer.so.9 /usr/local/cuda-12.1/lib64/libcudnn_ops.so.9
```

### GPU Lentíssima ou Modo CPU

```bash
# Verificar se usando GPU
python3 -c "from faster_whisper import WhisperModel; m = WhisperModel('tiny', device='cuda')"

# Se falhar, forçar CPU:
# Editar transcribe_batch.py: device = "cpu", compute_type = "int8"
```

---

## 📁 ESTRUTURA FINAL

```
~/videos/
├── aula_01.mp4
├── aula_01.srt (legenda)
├── aula_01_transcricao.txt  ← SAÍDA
├── aula_02.mp4
├── aula_02_transcricao.txt
├── cookies.txt
├── lista_videos.txt
└── lista_legendas.txt

~/faster_whisper_project/
├── venv/ (virtual env)
└── transcribe_batch.py
```

---

## 🎯 HEURÍSTICAS CRÍTICAS

| # | Regra | Impacto |
|---|-------|--------|
| 1 | `setup_cuda.sh` após formatação | GPU funciona |
| 2 | `wsl --shutdown` pós-script | Mudanças aplicadas |
| 3 | Ativar `venv` antes de rodar | Dependências corretas |
| 4 | Monitorar `nvidia-smi` em paralelo | Verificar GPU não trava |
| 5 | Cookies + Referer no yt-dlp | Download não é bloqueado |
| 6 | Legends manualmente se yt-dlp falhar | Fallback garantido |
| 7 | `torch.cuda.empty_cache()` no loop | Sem memory leak |

---

## ⏱️ TEMPOS

| Tarefa | Tempo |
|--------|-------|
| Setup Completo (primeira vez) | 1.5 horas |
| Download 10 vídeos 1080p | 20-30 min |
| Transcrição 10 horas de vídeo | 1-1.5 horas |
| **Total (primeira vez)** | **~3-4 horas** |

---

## 🔗 LINKS CRÍTICOS

- **Faster Whisper:** https://github.com/guillaumekln/faster-whisper
- **yt-dlp:** https://github.com/yt-dlp/yt-dlp
- **CUDA WSL:** https://learn.microsoft.com/en-us/windows/ai/directml/gpu-cuda-in-wsl
- **PyTorch:** https://pytorch.org/

---

## ✨ RESULTADO

✅ Vídeos baixados 1080p com cookies  
✅ Legendas .srt em PT-BR  
✅ Transcrições automáticas em GPU (5-10 min/hora)  
✅ Saída em .txt com timestamps

---

**STATUS:** ✅ Pronto para Execução  
**ÚLTIMA ATUALIZAÇÃO:** 26/10/2025 01:15  
**MANTIDO POR:** Auto

