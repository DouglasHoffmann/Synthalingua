# Guia de Configuração e Execução: AMD GPU (ROCm/OpenVINO) no Windows

Este guia detalha como utilizar o Synthalingua em sistemas Windows equipados com placas de vídeo AMD (como a série RX 7000), aproveitando tanto o suporte emergente ao ROCm quanto a estabilidade do OpenVINO.

---

## 🚀 Passos Iniciais

### 1. Requisitos de Driver
Certifique-se de estar usando o driver **AMD Software: Adrenalin Edition** mais recente. Para suporte a ROCm/AI no Windows, versões acima da 23.12.1 são altamente recomendadas.

### 2. Configuração do Ambiente (Virtual Environment)
Se você já possui um ambiente virtual (`venv`) com PyTorch e ROCm configurados:
1. Execute o arquivo `setup.bat`.
2. Escolha a **Opção 1: Use an existing Virtual Environment**.
3. Forneça o caminho da pasta do seu venv ou o caminho direto para o arquivo `activate.bat`.
   - *Exemplo:* `C:\Users\nome\Documents\venv\Scripts\activate.bat`
4. Quando perguntado sobre o tipo de GPU, escolha **2) AMD**.
5. Quando o script perguntar se deseja instalar o patch de PyTorch/ROCm, escolha **N** (Não), para preservar a instalação que você já possui e sabe que funciona.

---

## 🛠️ Como Executar

Após concluir o `setup.bat`, um arquivo chamado **`livetranslation.bat`** será gerado na raiz do projeto. Este é o seu atalho principal.

### Execução Padrão (Otimizada para AMD)
O atalho gerado utilizará automaticamente os seguintes parâmetros ideais para sua placa:
```bash
python synthalingua.py --model_source openvino --device amd-gpu
```

- **`--model_source openvino`**: Utiliza o motor da Intel que possui excelente suporte a GPUs AMD via drivers padrão no Windows. É o modo mais rápido e estável para placas Radeon.
- **`--device amd-gpu`**: Comando que implementamos para garantir que o OpenVINO identifique e utilize sua placa de vídeo AMD.

### Execução via ROCm Direto (Experimental)
Se você preferir usar o backend original do Whisper (OpenAI) com o ROCm que você instalou:
```bash
python synthalingua.py --model_source whisper --device cuda
```
*Nota: Mesmo sendo AMD, o PyTorch/ROCm emula a interface 'cuda' internamente.*

---

## 🐞 Solução de Problemas Comuns

### Erro: `No module named 'torch._C._distributed_c10d'`
Este erro é comum em builds de ROCm para Windows ao importar bibliotecas como `transformers`.
**Status:** Implementamos um "Workaround" automático no `synthalingua.py` que detecta e neutraliza esse erro no Windows, permitindo que o programa inicie normalmente.

### Performance Baixa
Se notar que a GPU não está sendo usada, verifique no Gerenciador de Tarefas (aba Desempenho > GPU) se há atividade em "Compute" ou "Graphics" durante a transcrição. Caso a performance esteja ruim, mude para `--model_source openvino`.

### Caminhos com Espaços
O `setup.bat` e o `livetranslation.bat` agora suportam caminhos com espaços (ex: `C:\Meus Projetos\venv`). Todas as chamadas internas foram protegidas com aspas.

---

## 📝 Resumo de Comandos Úteis

| Objetivo | Comando Recomendado |
| :--- | :--- |
| **Melhor Performance AMD** | `python synthalingua.py --model_source openvino --device amd-gpu` |
| **Maior Precisão (Modelo Large)** | Adicione `--ram 11gb-v3` ao comando acima |
| **Gerar Legendas de Arquivo** | Adicione `--makecaptions --file_input "video.mp4"` |
| **Interface Visual** | Adicione `--launchui` |

---
*Configurações aplicadas com sucesso para RX 7800 XT e similares.*
