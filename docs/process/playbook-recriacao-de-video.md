# Playbook: recriar um anúncio a partir de um vídeo de referência

Este é o processo operacional para o **Codex da OpenAI** analisar, decupar, recriar, montar e revisar um vídeo vertical de performance a partir de um vídeo de referência. Ele foi construído a partir de erros reais de análise e de geração, portanto dá prioridade à fidelidade, à classificação correta de cada take e à validação visual — não apenas a prompts bonitos.

## Ferramentas obrigatórias e função de cada uma

- **Codex/OpenAI:** coordena o processo, analisa o vídeo e os frames, escreve a decupagem e os prompts, gera ou edita imagens de referência para recriar takes e remover marca-d’água/legendas, e compara cada resultado ao original.
- **PalmierPro:** é o editor central. A IA deve montar tudo na timeline: cortes, duração, áudio, voice-over, zoom, pan, keyframes, composição, transições e exportação. Isso permite que o usuário acompanhe o anúncio enquanto ele é construído e faça ajustes simples diretamente na timeline.
- **Gerador de vídeo/lipsync aprovado:** é usado somente quando a cena realmente precisa de vídeo novo — especialmente uma pessoa falando ou movimento físico real. Nunca substitui a timeline do PalmierPro para zoom, corte, animação de foto ou composição de áudio.

O projeto não deve virar uma sequência de gerações isoladas. O PalmierPro é a fonte de verdade da montagem; arquivos em pastas ou na biblioteca não contam como cena pronta até estarem na timeline.

## Princípio central

Não trate o vídeo original como uma sequência de “avatares falando”. Trate-o como uma edição composta por tipos diferentes de mídia:

- depoimento em vídeo;
- vídeo de lifestyle ou b-roll;
- foto de antes/depois;
- foto animada na timeline;
- espelho/selfie;
- gravação de tela ou interface de app;
- tela gráfica/CTA;
- áudio de voz, ambiente, música e pausas.

Cada tipo pede uma técnica diferente. O maior erro de fidelidade é usar a mesma ferramenta para tudo.

## Resultado esperado e estrutura de projeto

Antes de gerar, criar esta estrutura no projeto. Não apagar versões anteriores; elas são parte do aprendizado e podem ser melhores que a última tentativa.

```text
projeto/
  source/
    video-original.mp4
    transcricao-original.txt
    clips-originais/
      scene-01-original.mp4
      scene-02-original.mp4
  decupagem/
    analise-cena-a-cena.md
    planilha-de-cenas.csv
  references/
    scene-01/
      original-start.jpg
      original-middle.jpg
      original-end.jpg
      clean-start-v1.png
      clean-middle-v1.png
      clean-end-v1.png
    scene-02/
      before-clean.png
      after-clean.png
  audio/
    scene-01-original.m4a
    scene-02-original.m4a
  generated/
    images/
    videos/
    archive/
  exports/
  docs/
```

Enquanto o editor estiver usando arquivos com caminhos absolutos, não mover os ativos de trabalho. Primeiro exportar um pacote auto-contido do projeto de edição; só então reorganizar/migrar a biblioteca com segurança.

### Organização quando o projeto contém vários criativos

Criar um índice por original em `assets/creative-<nome-do-criativo>/`, por exemplo `assets/creative-12/`, repetindo a estrutura `source/`, `takes/`, `references/`, `audio/`, `generated/`, `exports/` e `timeline/`. Esse índice deve usar links para os arquivos canônicos e para a biblioteca ativa do PalmierPro, não cópias. Assim, o criativo fica navegável em uma pasta única sem quebrar os caminhos que o editor já utiliza.

## Fase 0 — Preparar o material

1. Copiar o vídeo original para `source/` sem recomprimir.
2. Identificar duração, fps, resolução, proporção e se há áudio ambiente.
3. Extrair o áudio completo e uma transcrição aproximada.
4. Abrir um projeto PalmierPro com a proporção, fps e duração-base compatíveis com o original.
5. Criar uma folha de contato do vídeo inteiro. Ela permite identificar mudanças de pessoa, ambiente, enquadramento e tipo de mídia mais rapidamente do que ler uma transcrição.

Nunca assumir a natureza de uma cena apenas pelo texto falado. Uma frase pode continuar por cima de uma foto, uma interface ou uma nova pessoa.

## Fase 1 — Isolar takes e validar os cortes com um humano

Esta é a primeira etapa operacional e não pode ser pulada. Antes de gerar imagens, vídeos ou gastar créditos de IA, o Codex deve isolar os trechos exatos do original.

1. Assistir ao vídeo quadro a quadro quando houver dúvida e marcar cada **troca visual física**: mudança de pessoa, cenário, enquadramento, mídia, ação ou função gráfica.
2. Cortar e exportar um arquivo por cena física, preservando exatamente o frame inicial e final do take. Nomear em ordem: `scene-01-original.mp4`, `scene-02-original.mp4` e assim por diante.
3. Salvar os tempos de entrada e saída no vídeo original, em segundos e frames.
4. Para um antes/depois de fotos da mesma pessoa, manter tudo como **uma única cena**, ainda que haja uma troca de imagem: `scene-XX/take-01-before` e `scene-XX/take-02-after`. A troca é um segundo take dentro da mesma intenção narrativa, não uma nova cena independente.
5. Extrair três frames por take isolado: início, meio e fim. Em foto estática, extrair ao menos o frame completo mais representativo.
6. Apresentar ao humano uma lista clara dos cortes, com os tempos e os clips exportados.

### 1.1 Controle obrigatório de contaminação de frames

Depois de exportar, verificar visualmente o **primeiro e o último frame de cada clip**, não apenas a duração. Um corte pode parecer correto em segundos e ainda carregar um único frame da pessoa/cena anterior ou seguinte.

- se um frame não pertence ao take, ajustar a entrada/saída no limite físico do corte;
- se houver um flash, punch-in ou tela intermediária de poucos frames, preservar em `transitions/` como referência de edição, mas não classificá-lo como uma nova cena a ser gerada;
- quando um antes e depois estiver separado por uma transição curta, manter os dois takes na mesma pasta lógica e reproduzir a transição no PalmierPro;
- quando um rosto aparece no fim de uma demonstração de UI só para concluir uma palavra, registrar o áudio como voice-over de transição e não criar desnecessariamente uma cena de avatar.

### Portão humano 1 — aprovar cortes

O processo para aqui. O humano revisa os trechos e aponta, pelo tempo do vídeo original, onde qualquer corte ficou cedo, tarde ou separou equivocadamente uma mesma cena. O Codex corrige e reapresenta apenas os cortes afetados.

Somente após a aprovação explícita dos cortes é permitido criar referências limpas, prompts, imagens ou vídeos com ferramentas que consomem créditos.

## Fase 2 — Decupagem rigorosa

### 2.1 Encontrar cortes físicos, não só frases

Separar cenas por mudança visual real: pessoa, cenário, enquadramento, mídia ou função gráfica. Uma frase pode cruzar um corte. Se um depoimento começa em um take e termina sobre uma foto, são duas cenas visuais, porém um único fluxo de áudio.

Para cada cena, registrar:

| Campo | O que registrar |
| --- | --- |
| ID | `scene-01`, em ordem física no vídeo |
| Tempo | início/fim no vídeo original |
| Duração | em segundos e em frames da timeline |
| Fala | transcrição literal; marcar incertezas |
| Áudio | voz direta, voice-over, ambiente, música, silêncio |
| Tipo | vídeo falado, vídeo lifestyle, foto, montagem, UI, CTA |
| Pessoas | idade aparente, cabelo, roupa, pose, lado do quadro |
| Ambiente | objetos, fundo, profundidade, luz, horário |
| Câmera | quem segura, altura, lente, deslocamento, zoom, shake |
| Ação | movimentos em ordem cronológica |
| Texto/branding | texto, logo, legibilidade, posição |
| Método | lipsync, vídeo IA, foto na timeline, screen/UI, gráfico |
| Referências | quadros exatos necessários |
| Critérios de aceite | o que precisa estar certo para aprovar |

### 2.2 Descrever movimento como coreografia

Não escrever apenas “mulher bebendo”. Registrar a sequência exata:

1. posição inicial e direção do olhar;
2. objeto e mão que o segura;
3. gesto inicial;
4. instante em que a fala começa;
5. gesto que coincide com a fala;
6. deslocamento de câmera e sua velocidade;
7. posição final.

Exemplo de nível correto:

> Uma mulher está sentada no lado esquerdo do quadro em cadeira plástica azul. Segura um copo excepcionalmente grande de limonada amarela com gelo, rodelas de limão e canudo branco. Bebe primeiro; a câmera de outra pessoa começa mais distante e avança em velocidade constante até enquadrar rosto e busto. Ela solta o canudo, segura o copo com a mão direita, leva a mão esquerda ao peito e fala olhando para a lente. Ao fim, a câmera inicia um afastamento discreto. À esquerda há mar com ondas e duas pessoas; à direita, faixa de areia, palmeiras, quiosques distantes e um casal caminhando de costas.

Esse nível de detalhe é indispensável para geração fiel. “Resort com piscina, selfie, mulher falando” produz outro vídeo.

### 2.3 Perguntas obrigatórias de análise

Antes de criar qualquer prompt, responder para cada cena:

- É a própria pessoa que segura o celular ou existe um operador filmando?
- Há deslocamento real de câmera, zoom digital ou apenas mudança de corte?
- O movimento é fluido, contínuo ou uma animação de foto?
- A pessoa fala para a câmera, está em silêncio sob voz de outra pessoa ou é apenas uma foto?
- Há objetos pequenos importantes: celular na mão, canudo, copo grande, joia, quadro, roupa, app aberto?
- O fundo é parte da prova visual e precisa ser preservado?
- O áudio contém mar, rua, vento, sala, passos ou outro ambiente que precisa acompanhar a voz?

Se alguma resposta for “não sei”, marcar como pendência e assistir novamente. Não preencher por suposição.

## Fase 3 — Planejar a recriação e obter aprovação humana

Com os cortes aprovados, o Codex cria um plano de produção **cena a cena**, sem gerar ainda. O plano deve ser comparável ao original e indicar exatamente qual ferramenta fará cada parte.

Para cada cena, registrar:

| Campo | O que o plano precisa dizer |
| --- | --- |
| Objetivo | O que o espectador vê e entende naquela cena. |
| Takes | Quais clips/frames aprovados pertencem à cena, inclusive antes e depois. |
| Tipo e método | Lipsync/vídeo, foto na timeline, UI ou arte gráfica. |
| Referências | Frames de início, meio e fim; ou os dois frames de antes/depois. |
| Limpeza/regeneração | O que será removido e quais detalhes não podem mudar. |
| Movimento | Ação da pessoa e movimento de câmera; especificar o que será feito no PalmierPro. |
| Áudio | Áudio original embutido, voice-over, ambiente e ponto de corte. |
| Critérios de aceite | Elementos visuais e sonoros que serão comparados antes de aprovar. |

### Regras de planejamento por tipo

**Pessoa falando para a câmera**

- Separar prints do começo, meio e fim do movimento; analisar frames adicionais se houver dúvida sobre câmera, mãos ou expressão.
- Limpar marca-d’água/legenda somente quando permitido, preservando integralmente identidade, roupa, objetos, fundo e enquadramento.
- Descrever a cena nos mínimos detalhes antes de escrever o prompt.
- Identificar se há push-in, pull-back, pan ou zoom. O movimento de câmera deve ser planejado com keyframes no PalmierPro, não delegado ao vídeo gerado.
- Gerar a pessoa em enquadramento aberto apenas quando o original exige aproximação/zoom posterior. Se não houver movimento, o enquadramento inicial deve ser idêntico ao original — não deixar “folga” sem motivo.
- Usar o áudio original e preservar o ruído ambiente compatível (mar, vento, sala, rua) quando ele fizer parte da prova de realidade.

**Antes e depois em foto**

- Extrair uma referência do “antes” e uma do “depois”; regenerar/limpar ambas com a mesma riqueza de detalhes e sem marca-d’água quando autorizado.
- Declarar microdetalhes obrigatórios: celular na mão, roupa, quadro, acessórios, objeto de fundo, pose e crop.
- Medir no original quanto tempo cada estado permanece em tela. Quando o anúncio apresenta antes/depois de forma simétrica, manter **o mesmo tempo útil para a foto do antes e para a foto do depois**; a transição ocupa uma duração separada e curta. Nunca encurtar o “antes” só para antecipar a troca.
- Planejar a troca e qualquer pan/zoom no PalmierPro. Não gerar um avatar falante nem vídeo se o original é uma montagem de fotos.
- Manter a fala original como voice-over.

**Animações, efeitos e elementos gráficos**

- Antes de gerar, perguntar ao humano se a cena será criada pelo Codex ou se ele fornecerá o material final.
- Se o Codex for criar, solicitar logos, telas do app, tipografia, cores, textos aprovados e outros elementos necessários.
- Criar o plano visual e de timeline dessa cena e submetê-lo à mesma aprovação humana; UI e texto não devem ser inventados por um modelo de vídeo.

### Portão humano 2 — aprovar o plano

O humano aprova ou corrige o plano de cada cena antes da primeira geração. Nenhuma cena deve ser criada por inferência silenciosa quando o plano ainda não foi validado.

## Fase 4 — Classificar a técnica correta por cena

| Tipo visual | Técnica correta | O que evitar |
| --- | --- | --- |
| Pessoa falando para a câmera | imagem limpa + lipsync com áudio original | TTS genérico ou avatar com aparência diferente |
| Pessoa falando enquanto anda | vídeo/lipsync com prompt de caminhada; ou vídeo IA com referência | foto parada com voz se o movimento for importante |
| Foto de antes/depois | duas fotos limpas + corte/zoom/pan na timeline + voz original | transformar cada foto em avatar falante |
| Foto única de prova | foto limpa animada suavemente na timeline | gerar fala/lábios inexistentes |
| App no celular | screenshot/interface limpa + animação/recorte na timeline | pedir a um modelo de vídeo para escrever UI legível |
| Tela final/CTA | arte gráfica limpa montada no editor | vídeo IA para renderizar texto e logos |
| Lifestyle sem fala para a câmera | vídeo IA ancorado por quadro, ou foto animada se o original também parecer foto | lipsync no personagem |

### Regra de ouro

Se a boca da pessoa não é a fonte visual da fala, não usar lipsync nela. Preservar o áudio original como voice-over.

## Fase 5 — Referências visuais limpas

### 5.1 Quantas referências extrair

Para um take em vídeo com pessoa, ação ou movimento de câmera, extrair e limpar **as três referências**:

- início: composição e pose;
- meio: expressão/ação principal;
- fim: pose final, enquadramento e transição.

`clean-start-v1.png` é a âncora usual de geração; `clean-middle-v1.png` e `clean-end-v1.png` são obrigatórias para conferir ação, enquadramento e detalhes que poderiam se perder. Elas também devem ser disponibilizadas ao modelo quando a ferramenta aceitar múltiplas referências.

Para foto de transformação, usar uma referência limpa do “antes” e outra do “depois”. Para UI, extrair uma imagem limpa para cada estado de tela que aparece no corte. Uma foto rigorosamente estática precisa de apenas uma imagem limpa.

### 5.2 Limpar sem descaracterizar

Usar edição de imagem para remover, quando permitido e necessário:

- marca d’água;
- legenda sobreposta;
- sticker do app;
- texto de proteção;
- ruído ou compressão que impede o modelo de entender a cena.

O prompt de limpeza deve declarar invariantes. Exemplo:

> Remova somente a marca d’água diagonal, a legenda e o sticker. Preserve rigorosamente identidade, idade aparente, cabelo, óculos, roupa, corpo, pose, celular, fundo, luz, crop 9:16 e perspectiva. Não embeleze. Não adicione texto, logo ou objetos.

Depois, inspecionar a saída. A limpeza pode remover detalhes que o olho humano nota pouco, mas que importam para a réplica: um iPhone preto na mão, um quadro na parede, textura do maiô, objeto no fundo ou o tamanho desproporcional do copo.

### 5.3 Referência não é decoração

Uma descrição precisa melhora muito a geração, mas referência visual é necessária quando há identidade, roupa, pose, ambiente, composição ou objeto específico a preservar. Para alta fidelidade, usar ambas:

- imagem como âncora visual;
- prompt como especificação de ação, câmera e restrições.

## Fase 6 — Hospedar referências e áudio

Se a ferramenta de vídeo não aceita arquivos locais, disponibilizar as referências em HTTPS estável.

Procedimento recomendado:

1. Salvar `frame-clean.png` e `original-audio.m4a` por cena.
2. Publicar em repositório/armazenamento autorizado pelo usuário.
3. Confirmar que cada URL devolve HTTP 200 antes de enviar para o gerador.
4. Nunca depender de URL temporária sem verificar prazo de expiração.
5. Manter uma pasta de upload previsível, por exemplo `references/scene-15/`.

Não afirmar que um arquivo está disponível sem testar a URL. Um `404` deve ser tratado como bloqueio real, não como detalhe administrativo.

## Ciclo obrigatório de execução: uma cena por vez

### Monitor obrigatório de gerações assíncronas

Ao iniciar qualquer geração externa (HeyGen ou equivalente), o Codex deve criar imediatamente um monitor automático vinculado à tarefa. O monitor consulta o status até a conclusão, recupera o arquivo, compara com o take original, insere/substitui o resultado no PalmierPro, revisa as fronteiras vizinhas e só então informa a conclusão. **Nunca encerrar uma entrega em “geração iniciada”, “aguardando” ou “arquivo pronto”**: a entrega só termina com a cena montada e revisada na timeline.

Depois de aprovado o plano, o Codex não deve disparar a produção de todas as cenas de uma vez.

1. Criar e montar **somente a primeira cena planejada** no PalmierPro, com áudio e movimento de timeline quando aplicável.
2. Comparar a cena criada com seu clip original aprovado, em início, meio e fim.
3. Mostrar o resultado ao humano para aprovação e registrar os ajustes pedidos.
4. Corrigir a mesma cena até a aprovação, preservando versões anteriores que possam ser melhores.
5. Só então iniciar a cena seguinte e repetir o ciclo.

Esse ritmo evita escalar um erro de classificação — por exemplo, tratar uma montagem de fotos como avatar falante — para o anúncio inteiro. O humano continua vendo o filme se formar no PalmierPro e pode corrigir o processo cedo, antes de novos gastos com geração.

## Fase 7 — Gerar vídeos de depoimento

### 7.1 Usar o áudio original

Para depoimentos, preferir o áudio extraído do vídeo original em vez de TTS. Isso preserva:

- voz e pronúncia;
- pausas;
- velocidade;
- ruído de mar, rua, sala ou celular;
- emoção original;
- sincronismo com a montagem.

Se o vídeo gerado já contém esse áudio, silenciar/remover qualquer cópia de áudio independente na timeline para evitar duplicação ou eco.

### 7.2 Prompt de lipsync

O prompt não deve redescrever genericamente a pessoa. Deve travar identidade e limitar o que pode mudar.

Modelo:

```text
Vertical 9:16 authentic smartphone UGC testimonial.
Keep the adult person exactly as in the supplied clean reference: [identity markers, wardrobe, framing, background].
They deliver the supplied original audio with accurate, natural lip movements.
[specific permitted motion: natural blinks, tiny head movement, subtle walking sway].
Do not change the camera framing. Do not add people, objects, text, captions, logos or watermarks.
Preserve natural phone-camera exposure, casual compression and ordinary environment texture.
```

Se o original é filmado por outra pessoa, incluir explicitamente:

```text
The subject is not holding the phone. A second person operates the smartphone camera.
The camera performs a smooth constant-speed push-in from medium shot to medium close-up, then a very slight pull-back.
```

### 7.3 Movimento de câmera

Não esperar que o modelo interprete “zoom in” corretamente. Definir:

- quem segura a câmera;
- ponto inicial e final;
- duração relativa;
- direção;
- suavidade;
- o que deve permanecer no quadro.

Mesmo assim, se o movimento gerado não for convincente, não insistir em novo vídeo se a atuação já estiver correta. Usar o editor com timeline e keyframes para executar o zoom. O zoom de timeline deve ser progressivo, contínuo e com interpolação suave; um zoom linear/duramente interpolado parece tremido ou mecânico.

## Fase 8 — Vídeos lifestyle e movimento real

Quando o original mostra caminhada, cabelo reagindo ao passo, fundo deslocando-se ou uma ação física, uma foto com zoom não é equivalente. Ela pode servir de placeholder, mas deve ser marcada como temporária.

Para gerar vídeo a partir de imagem:

1. usar a imagem limpa aprovada como quadro inicial;
2. gerar somente a ação que falta;
3. descrever câmera e física, não recontar a aparência já presente na imagem;
4. preservar o áudio original separado, se a pessoa não estiver falando para a câmera.

Exemplos de prompts de movimento:

```text
One short continuous handheld smartphone shot. She takes two natural slow steps forward; her hair moves lightly with each step and the porch background shifts with real parallax. No speech, no lip-sync, no text.
```

```text
Short authentic smartphone clip. She tilts the tumbler to drink, lowers it naturally and makes a small relaxed gesture. Preserve the room and clothing. No dialogue, captions, logo or camera jump.
```

Se o editor de vídeo não tiver geração disponível, informar isso claramente antes de declarar o vídeo final pronto. Não esconder um placeholder como se fosse uma tomada em movimento.

## Fase 9 — Montagem na timeline do PalmierPro

### Regra de execução obrigatória

Antes de tocar em uma cena, abrir este playbook e reler as regras do tipo de mídia daquela cena. Depois de cada alteração, a revisão não pode se limitar ao novo elemento: inspecionar a janela que abrange **o fim da cena anterior, a cena alterada e o início da cena seguinte**. Como referência mínima, conferir pelo menos 1 segundo antes e 1 segundo depois de cada fronteira; quando o original tiver corte rápido, conferir quadro a quadro.

Esta regra evita dois erros recorrentes: cortar uma boa tomada anterior para encaixar um novo asset e deixar uma foto/áudio continuar além do ponto em que a fala ou o take original termina.

### 9.1 Configuração

- usar a mesma proporção do original: geralmente 9:16;
- definir fps compatível com o original ou com a timeline do editor;
- converter tempo para frames com precisão;
- criar faixas separadas para vídeo/imagens e áudio;
- manter áudio original alinhado às cenas.

### 9.2 Como montar cada tipo

**Depoimento em vídeo**

- colocar o lipsync no track de vídeo;
- usar o áudio embutido ou o áudio original, nunca ambos;
- ajustar duração ao corte original;
- verificar que lábios e palavras coincidem.

**Foto antes/depois**

- usar duas fotos limpas, não avatar;
- definir corte seco, dissolve curto ou troca no momento equivalente ao original;
- aplicar pan/zoom de poucos pontos percentuais;
- usar keyframes suaves;
- manter o áudio/voice-over original.
- em montagens antes/depois simétricas, distribuir a duração útil igualmente entre as duas fotos e reservar apenas alguns frames para a transição; conferir os frames e as durações antes de considerar a cena pronta;
- calcular a duração de cada foto a partir do take e do áudio original; a última foto deve terminar exatamente na fronteira da próxima cena, não permanecer em tela por conveniência;
- quando houver dissolve, validar o primeiro, cada quadro intermediário e o último quadro da transição; se a timeline não oferecer sobreposição de faixas, usar um microtake técnico de transição documentado, sem substituir as fotos por avatar ou vídeo IA.

**Foto de apoio**

- animação mínima: tipicamente escala de `1.00` para `1.03–1.06`;
- deslocamento pequeno e contínuo;
- sem “respiração” de zoom, tremor ou deslocamento aleatório.

**UI de celular**

- usar imagem/screenshot limpa;
- animar crop, pan ou zoom para sugerir navegação;
- nunca pedir para modelo de vídeo escrever texto de interface;
- se houver toque/dedo, preservar o dedo e sua posição original.

**CTA**

- usar arte gráfica limpa;
- manter mais tempo no fim caso o áudio termine antes;
- texto e logos devem vir de arte aprovada/editor, não de vídeo IA;
- conferir que o último frame não fica preto.

### 9.3 Zoom de alta qualidade

Para aproximar o rosto em um take existente:

- começar no enquadramento original;
- terminar somente no crop que o original mostra;
- usar curva `smooth`/ease-in-out leve, não linear dura;
- não aplicar shake artificial;
- manter a velocidade aproximadamente constante na maior parte do movimento;
- comparar início, meio e fim com o original.

Se a timeline permite, criar o movimento manualmente; é mais confiável que gerar um novo take só para conseguir zoom.

## Fase 10 — Controle de versões

Nunca sobrescrever versões aprovadas.

Convenção recomendada:

```text
scene-01-base-heygen.mp4
scene-01-zoom-v1.mp4
scene-01-zoom-v2-user-reference.mp4
scene-01-final-approved.mp4
```

Registrar por versão:

- prompt;
- referência usada;
- áudio usado;
- ferramenta/modelo;
- data;
- defeito observado;
- correção aplicada;
- decisão: rejeitada, backup, aprovada.

O “último arquivo” não é automaticamente o melhor. A versão anterior pode ter atuação melhor, enquanto a versão nova corrige apenas um movimento. Combinar as melhores partes na timeline quando possível.

## Fase 11 — Revisão em três passagens

Só exportar depois de concluir as três passagens.

### Passagem 1: integridade técnica

- toda cena está realmente na timeline?
- existem trechos em branco/preto?
- há mídia offline?
- há áudio duplicado, eco ou volume zerado errado?
- as durações e cortes não têm buracos?
- o CTA permanece até o final?
- cada fronteira cena anterior → cena atual → próxima cena foi conferida em frame inicial, frame final e uma reprodução curta? Nenhum take bom foi truncado por um asset posterior?

### Passagem 2: comparação visual cena a cena

Comparar início, meio e fim de cada cena criada contra os mesmos pontos no original.

Checklist:

- pessoa/roupa/cabelo/óculos corretos;
- posição no quadro e altura de câmera corretas;
- fundo e objetos essenciais preservados;
- mão correta segura o objeto;
- celular, copo, canudo, quadro, acessórios e demais microdetalhes presentes;
- ação física certa;
- movimento de câmera equivalente;
- luz e textura UGC críveis;
- UI e CTA não foram convertidos em avatares;
- sem marca d’água e sem legenda incorporada indevidamente.

### Passagem 3: ritmo e áudio

- a voz segue contínua entre cortes?
- o ambiente original está preservado onde importa?
- voz, ruído de praia, vento, rua ou sala combinam com a imagem?
- cenas de foto carregam a fala como voice-over, não lipsync indevido?
- pausas foram mantidas ou intencionalmente removidas?
- a transição visual acontece no momento correto da frase?
- a última imagem termina quando a fala/take original termina, sem cauda visual indevida antes da próxima cena?

Após cada passagem, registrar ajustes e repetir a inspeção afetada. Não dizer “pronto” se só os arquivos foram gerados, mas ainda não foram montados ou comparados.

## Fase 12 — Critério de entrega

O projeto só pode ser entregue quando todos estes itens forem verdadeiros:

- todas as cenas estão na timeline, em ordem;
- cada uma usa a técnica correta para seu tipo de mídia;
- a trilha sonora original está sincronizada e sem duplicação;
- não há segmentos pretos, lacunas ou placeholders não assumidos;
- as referências limpas estão salvas no projeto;
- os vídeos gerados estão salvos e nomeados em ordem;
- versões anteriores importantes foram preservadas;
- houve três revisões documentadas;
- a versão exportada foi conferida novamente em relação ao original;
- a limitação remanescente, se houver, foi declarada ao usuário antes da entrega.

## Erros que este playbook impede

1. **Confundir praia com piscina** por não olhar fundo, ondas, areia e pessoas distantes.
2. **Confundir câmera operada por outra pessoa com selfie.**
3. **Ignorar movimento de câmera** porque a composição está boa.
4. **Transformar foto de antes/depois em avatar falante.**
5. **Perder detalhes pequenos**, como celular preto na mão, quadro na parede, canudo, mão ou objeto do cenário.
6. **Usar a última geração ruim** em vez da melhor versão anterior.
7. **Criar zoom duro/tremido** fora de uma timeline de verdade.
8. **Substituir ambiente sonoro por áudio limpo demais.**
9. **Tratar referências na pasta como se já estivessem na timeline.**
10. **Dizer que está pronto sem comparar o resultado exportado com o original.**
11. **Editar uma cena isoladamente e cortar ou encobrir o fim da cena anterior.**
12. **Deixar uma foto além da fala ou omitir zoom/transição que existiam no original.**

## Checklist final para outra IA

```text
[ ] Li o vídeo inteiro e identifiquei cortes físicos.
[ ] Isolei e exportei todos os takes nos cortes físicos exatos.
[ ] Mantive antes/depois da mesma pessoa como uma cena com dois takes.
[ ] O humano aprovou os cortes ou corrigiu os tempos indicados.
[ ] Extraí início/meio/fim de todos os takes aprovados.
[ ] Classifiquei cada cena pelo tipo correto de mídia.
[ ] Descrevi pessoas, objetos, fundo, luz, ação e câmera sem generalizações.
[ ] Criei um plano por cena, incluindo referência, movimento no PalmierPro, áudio e critério de aceite.
[ ] O humano aprovou o plano antes de qualquer geração que consuma créditos.
[ ] Separei e preservei o áudio original por cena.
[ ] Criei e validei referências limpas.
[ ] Validei URLs de referência quando a ferramenta exige HTTPS.
[ ] Usei lipsync somente onde a pessoa realmente fala para a câmera.
[ ] Usei o PalmierPro para fotos, UI, CTA, cortes, áudio e zooms controlados.
[ ] Antes de editar, reli no playbook as regras do tipo de mídia da cena.
[ ] Após editar, inspecionei o fim da cena anterior, a cena atual e o início da próxima, incluindo os frames de fronteira.
[ ] A duração visual de fotos e vídeos termina exatamente junto do take/áudio original correspondente.
[ ] Reproduzi e conferi todo zoom, pan e transição que existe no original; não deixei movimento implícito de fora.
[ ] Concluí e obtive aprovação da cena atual antes de começar a próxima.
[ ] Mantive versões anteriores.
[ ] Verifiquei cada cena na timeline, não apenas na biblioteca.
[ ] Fiz três revisões: técnica, visual e áudio/ritmo.
[ ] Exporte somente após a validação final.
```

## Decisão simples para cada cena

```text
A boca da pessoa aparece falando a fala desta cena?
  Sim → referência limpa + lipsync com áudio original.
  Não → a fala é voice-over.

É uma foto/antes-depois?
  Sim → foto limpa + animação na timeline.

É interface ou CTA?
  Sim → usar screenshot/arte e editar na timeline.

É vídeo físico com caminhada, ação ou parallax visível?
  Sim → gerar/usar vídeo real ancorado por referência; não disfarçar foto estática como vídeo.
```

O objetivo não é “gerar algo parecido”. É reconstruir as decisões visuais e sonoras do original, cena por cena, usando a ferramenta certa em cada caso.
