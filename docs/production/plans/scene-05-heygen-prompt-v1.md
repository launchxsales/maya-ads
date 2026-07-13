# Cena 05 — prompt de geração v1

## Arquivos necessários no GitHub

Publicar em `references/scene-05/`:

- `clean-start-v1.png`
- `clean-middle-v1.png`
- `clean-end-v1.png`
- `original-audio-v2.m4a`

Arquivos locais correspondentes:

- `assets/reference-frames/canonical/scene-05/take-01/clean-v1/clean-start-v1.png`
- `assets/reference-frames/canonical/scene-05/take-01/clean-v1/clean-middle-v1.png`
- `assets/reference-frames/canonical/scene-05/take-01/clean-v1/clean-end-v1.png`
- `assets/audio/canonical/scene-05/original-audio-v2.m4a`

## Prompt

```text
Vertical 9:16 authentic low-resolution smartphone selfie testimonial. Use the supplied clean start image as the visual anchor and preserve the same woman exactly across the 1.33-second shot. She is the same person shown in the supplied before/after reference: natural middle-aged face, dark hair with gray streaks, lime-green zip jacket, close indoor phone selfie.

She says the supplied original audio with precise natural lip-sync: “I made a New Year’s resolution.” Keep only tiny natural head and eye movement; no camera movement or zoom. Preserve casual social-media compression, ordinary indoor lighting and imperfect UGC texture. Do not beautify her or turn this into studio footage. No captions, logos, watermarks, app icons, additional people or objects.

The image must cut cleanly to the tropical before photo in the next scene while retaining the identity established by the before/after pair.
```

## Geração planejada

- Formato: 9:16.
- Duração alvo: 1,33 s.
- Áudio: `original-audio-v2.m4a`, sem TTS e sem redução de ruído.
- Movimento: microexpressões e movimento mínimo de cabeça; sem zoom de timeline nesta cena.
- Versão de saída: `scene-05-lipsync-v2.mp4`.

## Resultado disponível para revisão

- Candidata preservada: `assets/creative-12/generated/candidates/scene-05-lipsync-v2.mp4`.
- Candidata de aparência UGC suavizada: `assets/creative-12/generated/candidates/scene-05-lipsync-v2-ugc-soft.mp4`.
- Ambas usam o áudio original. A versão suavizada reduz a nitidez excessiva da geração para se aproximar da compressão do material de origem.
- Estado: importada no PalmierPro como candidata; aguarda comparação humana antes de substituir o clipe legado na timeline.
