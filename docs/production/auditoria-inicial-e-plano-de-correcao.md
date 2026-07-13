# Auditoria inicial e plano de correção — Maya ADS

Data da auditoria: 13 de julho de 2026.

Este documento registra o estado real do projeto **antes** da nova execução do playbook. Ele não declara a recriação como pronta: serve para separar o que já existe, o que está na timeline e o que precisa ser validado/corrigido com o humano.

## 1. Estado confirmado

- Original: `source/video-original/12-original.mp4` — 59,179 s, 360×640, 30 fps.
- Timeline PalmierPro: 1080×1920, 25 fps, 1.479 frames (59,16 s).
- A faixa de vídeo da timeline está preenchida continuamente do frame 0 ao 1.479, sem lacuna visual registrada.
- Os trechos de áudio aparentam continuidade: onde não há clip independente na faixa A1, há áudio ligado ao clip de lipsync. Isso deve ser confirmado novamente na revisão de áudio final.
- O projeto contém 23 blocos visuais em sequência na timeline, mas ainda não contém o pacote de referências canônicas e aprovação de cortes exigidos pelo playbook revisado.

## 2. Organização criada nesta etapa

Os arquivos abaixo foram criados a partir do original, sem IA generativa:

```text
source/clips-original/
  scene-01-original.mp4 … scene-23-original.mp4

assets/reference-frames/canonical/
  scene-01/take-01/original-start.jpg
  scene-01/take-01/original-middle.jpg
  scene-01/take-01/original-end.jpg
  …
  scene-23/take-01/original-start.jpg
  scene-23/take-01/original-middle.jpg
  scene-23/take-01/original-end.jpg
```

Os tempos usados nestes cortes são a decupagem corrente e são **provisórios até a aprovação humana**. Os ativos ativos em `PalmierMedia/` não foram movidos: seus caminhos podem estar vinculados ao PalmierPro.

## 3. Desvios identificados em relação ao playbook

| Área | Estado atual | Correção necessária |
| --- | --- | --- |
| Cortes | Existiam frames soltos e intervalos aproximados, mas não uma pasta única de clips por cena. | Revisar os 23 clips provisórios e corrigir os timecodes apontados pelo humano. |
| Antes/depois | Há cenas montadas como foto única, embora o original possa ter dois takes dentro da mesma cena. | Após validar cada corte, separar `take-01-before` e `take-02-after` dentro da mesma pasta da cena. |
| Referências limpas | A maior parte das cenas tem apenas uma imagem limpa ativa. | Para takes de vídeo, criar `clean-start`, `clean-middle` e `clean-end`; para foto, criar a(s) imagem(ns) limpa(s) correspondente(s). |
| Registro operacional | Não havia planilha de cenas com estado, método e aprovação. | Criar/preencher `docs/production/planilha-de-cenas.csv` e atualizar a cada aprovação. |
| Análise histórica | Parte de `12-analise-detalhada.md` ficou anterior às correções visuais já descobertas durante o projeto. | Não apagar o histórico; registrar correções aprovadas na planilha e neste plano. |
| Biblioteca Palmier | Contém duplicatas e nomes herdados de etapas anteriores, mas é a biblioteca vinculada à timeline. | Não mover arquivos ativos. Organizar somente novos ativos na estrutura canônica e fazer uma migração apenas após reimportar/validar. |

## 4. Leitura da timeline atual e plano por bloco

| Cena | Frames na timeline | Método atual | Leitura objetiva | Próximo ajuste previsto |
| --- | ---: | --- | --- | --- |
| 01 | 0–108 | vídeo/lipsync + keyframes | Base HeyGen; zoom manual do usuário. | Preservar a melhor base e o zoom manual; comparar com os três frames canônicos. |
| 02 | 108–180 | fotos antes/depois | Duas fotos; a versão “depois” ativa já inclui iPhone preto e quadro. | Reclassificar em dois takes na estrutura canônica e conferir troca/tempo. |
| 03 | 180–223 | vídeo caminhada | Usa `v3-caminhada`. | Comparar parallax, cabelo e passo com o original antes de aprovar. |
| 04 | 223–318 | fotos antes/depois | Duas imagens com movimento de timeline. | Registrar dois takes e conferir se a troca reproduz o original. |
| 05 | 318–357 | lipsync | Vídeo de depoimento. | Criar trio limpo e validar boca, framing e áudio. |
| 06–08 | 357–459 | fotos animadas | Atualmente são imagens com keyframes. | Validar se o original é foto ou vídeo real; gerar movimento apenas se houver ação/parallax relevante. |
| 09 | 459–543 | lipsync | Vídeo de depoimento. | Criar trio limpo e validar ação do copo, câmera e áudio. |
| 10–11 | 543–681 | fotos animadas | Atualmente uma imagem por bloco. | Revalidar se há antes/depois/troca interna; separar takes se confirmado. |
| 12 | 681–725 | lipsync | Vídeo de depoimento. | Criar trio limpo e validar expressão, boca e enquadramento. |
| 13 | 725–814 | foto animada | Atualmente uma imagem. | Revalidar possível antes/depois e separar takes se confirmado. |
| 14 | 814–876 | lipsync | Vídeo de depoimento. | Criar trio limpo e validar. |
| 15 | 876–914 | lipsync | Vídeo curto de depoimento. | Criar trio limpo e validar. |
| 16 | 914–989 | lipsync | Depoimento; crop atual está mais fechado que o original. | Refazer referência limpa de plano mais aberto e recriar somente após aprovação do plano. |
| 17 | 989–1031 | foto animada | Foto de apoio com voice-over. | Validar classificação contra o clip isolado e manter foto se confirmada. |
| 18 | 1031–1066 | lipsync | Depoimento. | Criar trio limpo e validar. |
| 19–20 | 1066–1242 | fotos/UI | Foto de apoio e interface estática animada. | Conferir estados reais da interface e preservar voice-over; não usar avatar. |
| 21–22 | 1242–1391 | fotos animadas | O original apresenta movimento físico (caminhada/bebida); a timeline usa stills. | São placeholders: converter em vídeo real ancorado por referência quando a geração estiver disponível e aprovada. |
| 23 | 1391–1479 | CTA estático | Arte limpa com movimento leve. | Conferir texto, duração e último frame; manter como montagem de timeline. |

## 5. Ordem de execução a partir daqui

1. **Portão humano 1:** revisar os 23 clips em `source/clips-original/` e indicar, pelo tempo do original, qualquer corte incorreto.
2. Corrigir os clips e criar sub-takes para antes/depois ou mudanças internas confirmadas.
3. Preencher o plano por cena na planilha: técnica, referências, limpeza, áudio, movimento PalmierPro e critérios de aceite.
4. **Portão humano 2:** aprovar o plano antes de qualquer geração paga.
5. Executar uma cena por vez: criar, montar no PalmierPro, comparar início/meio/fim, receber ajuste humano e só então passar à próxima.

## 6. Limitações assumidas, não ocultas

- PalmierPro informa que a geração nativa está indisponível no momento (`canGenerate: false`). Não é possível converter os placeholders das cenas 21 e 22 em vídeo real por esse editor até a geração ser habilitada.
- Nenhum novo vídeo, imagem ou lipsync será disparado antes da aprovação dos cortes e do plano daquela cena.
- A presença de mídia em `PalmierMedia/` não equivale a uma referência canônica ou a uma cena aprovada; a timeline e a comparação com o original continuam sendo o critério.

## 7. Revisão humana dos cortes — aplicada (v2)

O humano revisou a primeira exportação de cortes e apontou contaminação de frames entre cenas. A versão anterior foi preservada em `source/clips-original/_v1-provisional/`. A estrutura ativa foi refeita a partir dos cortes físicos detectados quadro a quadro.

| Grupo lógico | Estrutura aplicada | Decisão |
| --- | --- | --- |
| 05 | `12,933333–14,266667` | Refeito como take curto, sem os frames de jardim/polca. |
| 06–07 | `scene-06-before-after/take-01-before` e `take-02-after` | Agrupados como uma única cena de antes/depois, conforme o playbook. |
| 08 | `17,533333–18,633333` | Mantido como foto única, para zoom lento de timeline. |
| 09 | `18,633333–21,866667` | Refeito sem os frames vizinhos. |
| 10 | dois takes: `21,866667–23,966667` e `23,966667–26,233333` | Antes/depois agora permanece em uma única cena lógica. |
| 11–12 | `scene-11-before-after/take-01-before` e `take-02-after` | Corrigidos como uma única cena lógica de antes/depois. |
| 13 | `take-01-bottle` e `take-02-speaking` | Duas pessoas, dois takes; não deve virar uma única geração. |
| 14–15 | `32,966667–35,366667` e `35,366667–36,866667` | Refeitos sem o frame vizinho inicial/final. |
| 16–17 | fala até `38,733333`; `scene-17/take-01-before` e `take-02-after` | A foto foi removida da cena 16 e a cena 17 foi dividida em antes/depois. |
| 18 | `41,566667–43,033333` | Corte aprovado pelo humano. |
| 19–20 | `scene-19-before-after/take-01-before` e `take-02-after`; UI inicia em `46,566667` | O “depois” foi removido da pasta de UI. O último rosto falando “It works” não será recriado; seu áudio será usado sobre a cena seguinte. |
| 21–23 | mantidos | Cortes aprovados pelo humano. |

Também foram preservados dois microtrechos visuais de transição em `source/clips-original/transitions/`. Eles são referência de edição, não cenas a serem recriadas. O áudio de transição da cena 20 foi salvo como `source/clips-original/transitions/scene-20-voiceover-to-scene-21.m4a`.

## 8. Aprovação de montagem — cenas 01 a 04

O humano aprovou visualmente as cenas 01, 02, 03 e 04 já presentes no PalmierPro. Elas foram marcadas como `ready` na planilha. Não substituir, reexportar ou alterar seus clips/keyframes sem uma solicitação específica; as novas referências canônicas são documentação para o processo, não motivo para sobrescrever uma versão aprovada.
