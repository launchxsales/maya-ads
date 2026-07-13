# Playbook: recriar um anúncio a partir de um vídeo de referência

Processo para o **Codex da OpenAI** recriar um anúncio vertical com fidelidade visual e sonora. O objetivo é reconstruir as decisões do original — mídia, ação, enquadramento, áudio, corte e ritmo — e não simplesmente gerar vídeos parecidos.

## 1. Princípios que não podem ser quebrados

- **PalmierPro é a fonte de verdade.** Uma cena só está pronta quando está montada, sincronizada e revisada na timeline. Arquivos gerados ou importados na biblioteca não são entrega.
- **Cada cena recebe a técnica adequada.** Não tratar todo take como “avatar falando”. Fotos, UI, CTA e vídeo com ação física são métodos diferentes.
- **O áudio original é a referência principal.** Preserve voz, pausas e ambiente. Na timeline, use o áudio original separado e silencie o áudio embutido de vídeos gerados para evitar eco.
- **Nunca inventar detalhes.** Se ação, corte, objeto, fala ou enquadramento estiverem incertos, rever o original quadro a quadro e registrar a dúvida.
- **Não substituir uma cena boa por uma tentativa pior.** Preservar candidatos e usar na timeline a melhor versão, não necessariamente a mais recente.
- **Toda alteração exige revisão das fronteiras.** Conferir a cena anterior → cena alterada → cena seguinte, incluindo primeiro e último frame.

## 2. Ferramentas e responsabilidade

| Ferramenta | Uso correto |
| --- | --- |
| **Codex/OpenAI** | Analisa, decupa, cria prompts, limpa/regenera referências, compara resultados e coordena o fluxo. A ferramenta de imagem da OpenAI é usada para remover marca-d’água/legendas e recriar referências limpas. |
| **PalmierPro** | Editor central: cortes, duração, áudio, voice-over, keyframes, zoom, pan, transições, composição, revisão e exportação. |
| **HeyGen ou gerador aprovado** | Somente para vídeo novo com pessoa falando ou ação física que não pode ser reproduzida por uma foto animada. |
| **GitHub/HTTPS autorizado** | Hospeda referências limpas e áudios quando o gerador externo não aceita arquivos locais. |

Não usar IA de vídeo para escrever UI, CTA, logos ou textos legíveis. Isso pertence a screenshots/arte aprovada e à timeline.

## 3. Organização de arquivos

Para cada original, usar uma pasta única. Exemplo para o criativo 12:

```text
assets/creative-12/
  source/
    video-original.mp4
    clips-original/
      scene-01/
        take-01-original.mp4
  references/
    canonical/
      scene-01/
        take-01/
          original-start.jpg
          original-middle.jpg
          original-end.jpg
          clean-v1/
            clean-start-v1.png
  audio/
    canonical/
      scene-01/
        take-01-original-audio-v1.m4a
  generated/
    candidates/
      scene-01-lipsync-v1.mp4
      scene-01-lipsync-v2.mp4
  exports/
docs/
  process/
  decupagem/
```

Regras:

- Guardar o original sem recompressão e não sobrescrever candidatos.
- `original-start/middle/end` são sempre referências de análise para vídeo; não precisam ser limpas todas.
- Para vídeo gerado, `clean-start` é normalmente a âncora obrigatória. Limpar meio/fim apenas se forem necessários para preservar uma ação, um detalhe, o enquadramento final ou uma transição.
- Para antes/depois, criar uma referência limpa para cada estado (`before` e `after`).
- Não mover assets já ligados ao PalmierPro sem uma migração controlada; caminhos quebrados tornam a timeline frágil.

## 4. Fluxo completo

### Etapa A — Preparar e decupar o original

1. Inspecionar duração, fps, resolução, proporção e áudio ambiente.
2. Assistir ao vídeo inteiro e marcar **cortes físicos**: mudança de pessoa, cenário, enquadramento, mídia, ação ou função gráfica. Não cortar apenas porque a frase mudou.
3. Exportar um clip por cena/take físico com entrada e saída exatas; registrar tempos em segundos e frames.
4. Extrair frames de início, meio e fim de cada take em vídeo.
5. Conferir o primeiro e o último frame de cada clip. Corrigir qualquer contaminação de um frame da cena anterior ou seguinte.

Regras de agrupamento:

- Antes/depois da mesma pessoa é **uma cena com dois takes**, mesmo havendo troca de foto.
- Flash, dissolve ou frame técnico de poucos quadros é transição, não uma nova cena a gerar.
- Se uma pessoa aparece por poucos frames no fim de uma UI apenas para concluir uma palavra, usar a voz como transição/voice-over; não criar um novo avatar sem necessidade.

### Portão humano 1 — cortes

Antes de gastar créditos, o humano revisa os clips isolados e sinaliza os tempos de cortes errados. Corrigir somente os afetados e aguardar aprovação explícita.

### Etapa B — Analisar e planejar cada cena

Criar uma planilha ou tabela de cenas. Ela é obrigatória porque evita classificação errada e serve de controle de montagem.

| Campo | Registrar |
| --- | --- |
| ID e tempo | Ordem física, início/fim e duração em frames. |
| Tipo | Vídeo falado, ação/lifestyle, foto, antes/depois, UI, gráfico ou CTA. |
| Visual | Pessoa, roupa, cabelo, objetos, fundo, luz, composição e microdetalhes. |
| Câmera | Operador ou selfie, altura, enquadramento, shake, pan, push-in/pull-back. |
| Ação | Sequência cronológica: mãos, objeto, olhar, fala, movimento corporal e fim. |
| Áudio | Fala literal, voice-over, ambiente, música, pausas e fronteira do áudio. |
| Método | Técnica que será usada e o que será feito no PalmierPro. |
| Critério de aceite | O que precisa coincidir com o original. |

Perguntas que devem estar respondidas antes do prompt:

- Quem opera a câmera? Há zoom real, movimento de câmera ou só corte?
- A boca da pessoa é a fonte visual do áudio? Ou a fala é voice-over sobre foto/UI?
- Há movimento físico indispensável — caminhada, cabelo reagindo, parallax, beber, girar, objeto vindo até a câmera?
- Quais pequenos elementos provam fidelidade: celular, canudo, copo, quadro, joia, roupa, app, mão correta?
- O ambiente sonoro faz parte da realidade da cena?

### Portão humano 2 — plano

Apresentar o plano de **todas** as cenas antes de gerar. Para cada uma, o humano deve enxergar: referências, técnica, ação, áudio, movimento de timeline e critérios de aceite. Depois da aprovação, executar e aprovar **uma cena por vez**.

## 5. Escolher a técnica certa

| O original mostra | Fazer | Não fazer |
| --- | --- | --- |
| Pessoa falando para a câmera | Referência limpa + lipsync/vídeo ancorado pelo áudio original. | TTS genérico, avatar diferente ou crop alterado. |
| Pessoa falando enquanto caminha ou executa ação física | Vídeo ancorado por referência com a ação explícita; áudio original separado. | Foto parada com voz quando cabelo, corpo ou fundo se movem. |
| Foto única | Foto limpa + animação mínima no PalmierPro. | Inventar fala/lábios. |
| Antes/depois | Duas fotos limpas + cortes/transição/keyframes no PalmierPro + voice-over. | Transformar fotos em avatar falante. |
| App/UI de celular | Screenshot limpo para cada estado + crop/pan/zoom na timeline; preservar dedo se existir. | Pedir para IA de vídeo renderizar interface ou texto. |
| CTA/gráfico | Arte aprovada no editor. | Gerar texto ou logo por vídeo IA. |
| Lifestyle sem fala visível | Vídeo ancorado por referência; foto animada apenas se o original também for estático. | Lipsync desnecessário. |

Regra simples: **se a boca não é a origem visual da fala, o áudio deve ser voice-over.**

## 6. Referências limpas e prompts

### Limpeza

Usar a ferramenta de imagem da OpenAI para remover marca-d’água, legenda e sticker somente quando necessário. O prompt de edição deve travar invariantes:

```text
Remova somente marca-d’água, legenda e sticker.
Preserve identidade, proporções faciais/corporais, cabelo, roupa, objeto, mãos,
fundo, luz, enquadramento 9:16 e perspectiva. Não embeleze, não recorte,
não adicione texto, logo, pessoas ou objetos.
```

Validar a referência limpa contra o frame original antes de gerar vídeo. Em pessoa falando, comparar: formato do rosto, tamanho da cabeça, ombros/torso visíveis, cabelo, roupa e bordas do cenário. Se a limpeza deformar ou recortar, ela não serve como âncora.

### Prompt de pessoa falando

Descrever apenas o que precisa acontecer além da referência: lipsync, microexpressões e movimento permitido. Não pedir zoom ao gerador quando ele pode ser feito no PalmierPro.

```text
Vertical 9:16 authentic smartphone testimonial. Keep the supplied clean reference
exactly: identity, wardrobe, framing and background. Use the supplied original audio
with natural lip sync, blinks and subtle head movement only. Static phone camera.
No crop change, no facial/body distortion, no text, logos, watermarks or new objects.
```

Para ação física, descrever a coreografia em ordem e a física do movimento:

```text
One continuous fixed-phone UGC take. She finishes a sip, lowers the tumbler,
smiles, then extends the same tumbler toward the camera. Preserve the room and
clothing. Natural hair and sleeve movement. No cuts, text, watermark or camera zoom.
```

### Hospedagem

Quando o gerador externo exigir HTTPS, publicar **a referência limpa e o áudio original** em GitHub ou armazenamento autorizado. Testar a URL antes de enviar. Não usar URL temporária como referência permanente.

## 7. Execução por cena

1. Gerar somente a cena aprovada.
2. Se a geração for assíncrona, criar imediatamente um monitor automático que: espera, recupera o arquivo, salva em `generated/candidates`, importa no PalmierPro, monta, revisa e só então informa conclusão.
3. Inspecionar o candidato no início, meio e fim contra o original. Rejeitar distorção, ação errada, crop diferente, objeto ausente ou texto/marca d’água.
4. Montar no PalmierPro usando a duração original em frames.
5. Usar o áudio original separado e silenciar o áudio embutido do vídeo gerado.
6. Revisar pelo menos 1 segundo antes/depois da cena; em corte rápido, conferir quadro a quadro.
7. Pedir aprovação humana da cena e registrar o ajuste. Só então avançar.

Uma geração pronta não é uma entrega. A entrega da cena é: **arquivo salvo + cena montada + áudio alinhado + fronteiras revisadas**.

## 8. Regras de timeline no PalmierPro

### Áudio e duração

- Não duplicar áudio: escolher o áudio original separado e mutar o embutido.
- A imagem/vídeo deve terminar no mesmo ponto narrativo do áudio original, salvo quando o original mantém deliberadamente uma imagem em silêncio.
- Se o usuário cortar o fim defeituoso de um vídeo gerado, manter apenas os frames bons e reduzir a velocidade desse trecho para preencher a duração do áudio; não reintroduzir a deformação.
- Preservar ruído de mar, vento, sala, rua ou celular quando ele existir no original.

### Fotos e antes/depois

- Em antes/depois simétrico, dar o mesmo **tempo útil** ao antes e ao depois; reservar poucos frames para a transição.
- A última foto termina na fronteira correta, não permanece por conveniência.
- Usar zoom/pan discreto e keyframes suaves. Foto de apoio costuma variar de escala `1.00` para `1.03–1.06`.

### Câmera e movimento

- Zoom, pan, push-in e pull-back devem ser feitos por keyframes no PalmierPro sempre que forem movimento de edição, não ação física.
- Usar interpolação suave/ease; evitar zoom linear duro, shake artificial e cortes de crop bruscos.
- Começar e terminar nos enquadramentos do original. Não abrir o quadro “para sobrar espaço” se o original é estático.

### UI e CTA

- UI: animar screenshots limpos com recorte, pan ou zoom. Preservar dedo/toque se fizer parte do take.
- CTA: usar arte aprovada e conferir o último frame; não deixar preto, lacuna ou texto improvisado.

## 9. Revisão final e entrega

Fazer três passagens antes de exportar:

1. **Técnica:** cenas na timeline, sem preto/lacuna/offline, sem áudio duplicado, duração correta e CTA/fim íntegros.
2. **Visual:** comparar início/meio/fim de cada cena ao original: pessoa, roupa, objeto, mãos, fundo, luz, ação, enquadramento e movimento.
3. **Ritmo e áudio:** voz contínua, ambiente coerente, voice-over nas cenas corretas, transições no ponto da frase e fim visual alinhado ao áudio.

Só declarar o projeto pronto quando:

- todas as cenas estiverem na timeline em ordem;
- cada cena usar a técnica adequada;
- referências limpas, áudios e candidatos estiverem salvos e nomeados;
- a versão exportada tiver sido comparada ao original;
- limitações remanescentes tiverem sido declaradas.

## Checklist compacto

```text
[ ] Isolei cenas/takes nos frames exatos e o humano aprovou os cortes.
[ ] Mantive antes/depois como uma cena com dois takes.
[ ] Analisei pessoa, objetos, ação, câmera, áudio e microdetalhes de cada cena.
[ ] O humano aprovou o plano por cena antes de gastar créditos.
[ ] Extraí original-start/middle/end; criei apenas as referências limpas necessárias.
[ ] Escolhi técnica correta: vídeo, foto, antes/depois, UI ou CTA.
[ ] Usei referência visual + prompt de ação/restrições para geração de vídeo.
[ ] Usei o PalmierPro para corte, áudio, zoom, pan, transição e composição.
[ ] Mutei áudio embutido e mantive áudio original separado/alinhado.
[ ] Após cada edição, revisei cena anterior → atual → próxima, inclusive fronteiras.
[ ] Preservei candidatos e só usei a melhor versão.
[ ] Fiz revisão técnica, visual e de ritmo/áudio antes de exportar.
```
