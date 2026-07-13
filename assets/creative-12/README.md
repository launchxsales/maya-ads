# Criativo 12 — índice sincronizado de ativos

Esta pasta reúne os ativos do vídeo original `12.mp4` na estrutura operacional do playbook, sem duplicar os arquivos de trabalho.

| Pasta | Conteúdo | Sincronização |
| --- | --- | --- |
| `source/` | vídeo original | link para `source/video-original/12-original.mp4` |
| `transcript/` | legenda/transcrição original | link para `source/transcripts/12-subtitles.txt` |
| `takes/` | cortes revisados por cena e take | link para `source/clips-original/` |
| `references/frames/` | frames de início, meio, fim e referências limpas por take | link para `assets/reference-frames/canonical/` |
| `audio/canonical/` | áudio recortado de takes revisados | link para `assets/audio/canonical/` |
| `audio/palmier-active/` | áudio ativo na edição | link para `PalmierMedia/Audio/` |
| `generated/videos-active/` | vídeos que podem estar sendo usados na timeline | link para `PalmierMedia/Videos/` |
| `generated/legacy-outputs/` | versões históricas preservadas | link para `generated/legacy-outputs/` |
| `exports/` | exportações finais aprovadas do criativo | pasta local do criativo; vazia até a primeira renderização final |
| `timeline/palmier-media-active/` | biblioteca externa vinculada ao PalmierPro | link para `PalmierMedia/` |

## Regra de segurança

Os links mantêm esta pasta sincronizada com o projeto e com a biblioteca ativa do PalmierPro. Não substituir esses links por cópias nem mover/renomear arquivos em `PalmierMedia/` sem reimportar e validar a timeline.

Para um novo criativo, criar outra pasta no mesmo nível, por exemplo `assets/creative-13/`, mantendo essa mesma estrutura.
