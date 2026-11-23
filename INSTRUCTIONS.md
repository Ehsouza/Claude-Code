# Instruções: edição do vídeo de calibração Eightfold

> Briefing para uma conversa nova. Leia tudo antes de começar e siga o fluxo abaixo.

## Contexto
- Vídeo de **calibração da Eightfold**, ~12 minutos, ~500 MB, gravação de tela com narração.
- Áudio em **português**, com vários **termos em inglês** (termos de Eightfold/Admin Console, calibração etc.).
- O vídeo está nesta pasta (confirmar o nome do arquivo com o usuário se houver mais de um).
- Provavelmente contém dados de cliente/candidatos: **não fazer upload para nenhum serviço externo**. Processar tudo localmente.

## Objetivo
Destacar ("highlight") na tela os elementos sobre os quais o usuário está falando, no momento em que ele fala deles.
Exemplo: se aos 03:12 ele diz "skill match threshold" e esse campo está visível, desenhar uma caixa em volta do campo enquanto ele fala.

Também pode ter textos de apoio na tela, quando fizer sentido.

## Preferências já definidas
- Estilo do destaque: **caixa colorida (colored box)** em volta do elemento. Sem spotlight/zoom, a menos que o usuário peça.
- **Sem branding** (sem logo, sem cores de marca). Visual limpo e neutro.
- Comunicação com o usuário em português.

## Fluxo de trabalho
1. **Setup**: verificar ou instalar `ffmpeg`, `tesseract` e os pacotes Python `openai-whisper`, `pytesseract` e `opencv-python-headless`. Pedir aprovação antes de instalar.
2. **Transcrição**: Whisper com `language="pt"` e `word_timestamps=True`. Usar um modelo bom para mistura PT/EN (`medium` ou `large-v3`, se a máquina aguentar; se ficar lento, usar `small`). Salvar a transcrição com timestamps (JSON + SRT) nesta pasta.
3. **Análise da tela**: extrair frames nos momentos relevantes (ffmpeg) e rodar OCR (tesseract com `por+eng`) para pegar o texto e as coordenadas (bounding boxes) dos elementos visíveis.
4. **Cruzar fala com tela**: quando um termo falado bate com um texto visível no frame, aquele trecho vira candidato a destaque. Para elementos sem texto (ícones, "isso aqui"), olhar o frame e estimar a posição, marcando o item como "precisa conferir".
5. **Revisão antes de renderizar (obrigatória)**: mostrar ao usuário uma lista assim:
   `03:12–03:20 | caixa em "Skill Match Threshold" | (x, y, w, h)`
   O usuário aprova, remove ou ajusta. **Não renderizar antes da aprovação.**
6. **Render**: aplicar as caixas com ffmpeg (`drawbox` com `enable='between(t,início,fim)'`), mantendo o áudio original. Gerar primeiro um trecho curto de teste, se for útil, e depois o vídeo completo. Saída em MP4 (H.264 + AAC) nesta pasta, sem sobrescrever o original.
7. **Iterar** com base no feedback.

## Atenção
- Se a tela rolar ou mudar enquanto o usuário fala, a posição da caixa precisa acompanhar. Checar frames no início, no meio e no fim de cada trecho.
- Nunca modificar nem apagar o vídeo original.
