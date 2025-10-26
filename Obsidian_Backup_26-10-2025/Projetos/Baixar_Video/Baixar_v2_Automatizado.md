# 📥 DIÁRIO BAIXAR VIDEO - VERSÃO 2.0 AUTOMATIZADO

**Versão:** 2.0 (Otimizada para Automação)  
**Data:** 26 de outubro de 2025  
**Status:** Pronto para Download em Batch  
**Ambiente:** Windows 11 + WSL2 Ubuntu + yt-dlp + Firefox Extensions

---

## 🎯 EM 30 SEGUNDOS

Automatizar download de vídeos HLS protegidos por cookies + legendas PT-BR em batch via yt-dlp + Firefox.

**Setup:** 30 min  
**Download 13 vídeos 1080p:** 20-30 min (depende largura banda)

---

## 📋 FERRAMENTAS NECESSÁRIAS

| Ferramenta | Onde | Função |
|-----------|------|--------|
| **yt-dlp** | WSL Ubuntu | Download vídeos + m3u8 |
| **wget** | WSL Ubuntu | Download legendas |
| **ffmpeg** | WSL Ubuntu | Conversão/Processamento |
| **cookies.txt (ext)** | Firefox | Exportar cookies autenticados |
| **HLS Stream Detector (ext)** | Firefox | Detectar URLs de playlist |

---

## 🔧 SETUP RÁPIDO - 3 PASSOS

### PASSO 1: Firefox + Extensões (5 min)

1. **Firefox → Add-ons**
2. Pesquisar e instalar:
   - `cookies.txt` (versão 0.7.2+)
   - `HLS Stream Detector` (última versão)
3. Reabrir Firefox

### PASSO 2: Capturar URLs (10 min)

**No Firefox:**

1. Login na plataforma (ex: posartmed.grupoa.education)
2. Clique na extensão **cookies.txt** → **Export** → Salvar `cookies.txt` em Downloads
3. Reproduza **UM vídeo** até aparecer playlist
4. Clique extensão **HLS Stream Detector**
5. Copie URL da playlist-1080p.m3u8
6. Copie URL da legenda pt-br.srt
7. Salve ambas em **links.txt** no Windows

### PASSO 3: Transferir para WSL (5 min)

```bash
# No WSL Ubuntu
cd ~/videos

# Copiar files do Windows
cp "/mnt/c/Users/[seu-usuario]/Downloads/links.txt" .
cp "/mnt/c/Users/[seu-usuario]/Downloads/cookies.txt" .

# Verificar
ls -la links.txt cookies.txt
```

---

## 🎬 FLUXO DE DOWNLOAD

### Gerar Listas Automáticas

```bash
cd ~/videos

# Extrair URLs de vídeos
grep -o 'https://[^ ]*playlist-1080p\.m3u8[^ ]*' links.txt > lista_videos.txt

# Extrair URLs de legendas
grep -o 'https://[^ ]*pt-br\.srt[^ ]*' links.txt > lista_legendas.txt

# Contar linhas (quantos vídeos?)
wc -l lista_videos.txt lista_legendas.txt
```

### Download de Vídeos em Batch

```bash
cd ~/videos

# Usar arquivo cookie + referer + multi-thread
yt-dlp -a lista_videos.txt \
       -o "aula_%(autonumber)02d.mp4" \
       --cookies cookies.txt \
       --referer "https://[SEU-DOMINIO]" \
       --user-agent "Mozilla/5.0 (X11; Linux x86_64; rv:132.0) Gecko/20100101 Firefox/132.0" \
       --concurrent-fragments 4

# Monitorar
echo "✅ Download completo!"
ls -lh *.mp4 | wc -l
```

### Download de Legendas

**Problema:** wget com `-i` (arquivo lista) pode não funcionar bem com legendas

**Solução 1: Download individual (seguro)**

```bash
cd ~/videos

# Ler arquivo linha por linha
while IFS= read -r url; do
    [ -z "$url" ] && continue
    # Extrair nome legenda (aula_01.srt, etc)
    name=$(echo "$url" | grep -o 'aula_[0-9]*' | head -1)
    [ -z "$name" ] && name="aula_$(date +%s)"
    
    echo "📥 Baixando: $name.srt"
    wget -q -O "$name.srt" "$url"
done < lista_legendas.txt

echo "✅ Legendas baixadas!"
```

**Solução 2: Wget direto (mais rápido)**

```bash
cd ~/videos
wget -i lista_legendas.txt -P .

# Limpar nomes com caracteres estranhos
for f in *.srt*; do
    [ "$f" != "*.srt*" ] && mv "$f" "$(basename "$f" | cut -d'?' -f1)"
done
```

---

## ✅ CHECKLIST FINAL

```bash
cd ~/videos

# Verificar vídeos
ls -lh *.mp4
# Esperado: aula_01.mp4, aula_02.mp4, ... aula_NN.mp4

# Verificar legendas
ls -lh *.srt
# Esperado: aula_01.srt, aula_02.srt, ... aula_NN.srt

# Contar
echo "Vídeos: $(ls *.mp4 2>/dev/null | wc -l)"
echo "Legendas: $(ls *.srt 2>/dev/null | wc -l)"

# Se números forem iguais: ✅ Pronto para transcrição!
```

---

## 🛠️ TROUBLESHOOTING

### Erro: "403 Forbidden" ao baixar

```bash
# Causa: Cookie expirado ou referer inválido
# Solução: Reexportar cookies.txt
# No Firefox: cookies.txt extension → Export → Resubstituir arquivo

# Verificar se cookie contém token autenticação:
head cookies.txt
# Deve ter muitas linhas com valores (não vazio)
```

### Erro: "playlist-1080p.m3u8 não encontrado em links.txt"

```bash
# Causa: Você copiou URL de SD/720p, não 1080p
# Solução:
grep 'playlist' links.txt  # Listar todas as playlists encontradas
# Procurar por "1080p" específicamente
```

### Wget baixa arquivo HTML ao invés de .srt

```bash
# Causa: URL de legenda redireciona (com token)
# Solução: Usar yt-dlp no lugar
yt-dlp "[URL-LEGENDA]" -o "aula_01.srt" --write-subtitle --sub-lang pt

# Ou usar curl com follow-redirects
curl -L "[URL-LEGENDA]" -o "aula_01.srt"
```

---

## 📊 DESEMPENHO ESPERADO

| Tamanho | Vídeos | Download (4 threads) |
|---------|--------|---------------------|
| 1 hora cada | 13 | 20-30 min (50 Mbps) |
| Legendas | 13 | 1-2 min |
| **Total** | **~2GB** | **~25 min** |

---

## 🔗 ESTRUTURA FINAL

```
~/videos/
├── aula_01.mp4
├── aula_01.srt (legenda)
├── aula_02.mp4
├── aula_02.srt
├── ...
├── cookies.txt
├── links.txt (backup)
├── lista_videos.txt
└── lista_legendas.txt
```

---

## 🎯 HEURÍSTICAS CRÍTICAS

| # | Regra | Por Quê |
|---|-------|--------|
| 1 | **Cookies exportados SEMPRE** | Autenticação necessária |
| 2 | **Referer correto** | Sites anti-bot bloqueiam |
| 3 | **`--concurrent-fragments 4`** | Acelera download HLS |
| 4 | **Legendas manualmente se wget falhar** | yt-dlp é fallback |
| 5 | **Verificar nomes depois** | Corrigir duplicatas/erros |
| 6 | **Manter links.txt como backup** | Redownload se necessário |
| 7 | **Limpar caracteres especiais em .srt** | Evita problemas de encoding |

---

## ⏱️ TEMPOS

| Tarefa | Tempo |
|--------|-------|
| Setup + Captura URLs | 30 min |
| Download 13 vídeos 1080p | 20-30 min |
| Download 13 legendas | 1-2 min |
| **Total (primeira vez)** | **50-60 min** |

---

## 🔗 LINKS CRÍTICOS

- **yt-dlp:** https://github.com/yt-dlp/yt-dlp
- **Firefox Extensions:** https://addons.mozilla.org/
- **cookies.txt Extension:** Direct link from Firefox Store
- **HLS Stream Detector:** Direct link from Firefox Store

---

## ✨ RESULTADO

✅ 13+ vídeos 1080p baixados  
✅ Legendas PT-BR alinhadas  
✅ Nomes padronizados (aula_NN.mp4 + aula_NN.srt)  
✅ Pronto para transcrição GPU

---

## 🚀 INTEGRAÇÃO COM TRANSCRIÇÃO

Após este passo, você pode rodar:

```bash
# (Ver Diario_Transcrição_v2_Pratica.md)
cd ~/faster_whisper_project
source venv/bin/activate
python3 transcribe_batch.py
```

Isso gerará:
```
~/videos/aula_01_transcricao.txt
~/videos/aula_02_transcricao.txt
...
```

---

**STATUS:** ✅ Pronto para Execução  
**ÚLTIMA ATUALIZAÇÃO:** 26/10/2025 01:18  
**MANTIDO POR:** Auto

