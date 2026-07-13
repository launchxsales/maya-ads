# Cena 05 — plano de correção para aprovação humana

## Problema confirmado

O take original é um selfie de baixa resolução, fortemente filtrado/comprimido. A geração atual não preserva de forma convincente a identidade que deve fazer continuidade com o antes/depois da cena 06. A falha vem da pouca informação facial do take de origem, não de um detalhe de movimento.

## Estratégia de referência dupla

1. Usar os três frames da cena 05 somente para preservar **enquadramento e contexto**: selfie muito fechada, casaco/camisa verde-limão, cabelo preso/escuro com mechas acinzentadas, ambiente interno, luz frontal e compressão de celular.
2. Usar o take “antes” da `scene-06-before-after` como âncora de **identidade corporal/facial**: mulher sentada em cadeira branca, cabelo escuro, roupa preta de bolinhas brancas e cenário tropical. Essa é a referência visual mais confiável para tornar os dois depoimentos a mesma pessoa.
3. Criar imagens limpas separadas para cada finalidade, sem “embelezar” nem trocar roupa/cenário.

## Método de recriação

- Gerar um vídeo de lipsync de 1,333 s usando o áudio original da cena 05.
- A mulher precisa parecer a mesma pessoa do antes/depois da cena 06, mas gravada em selfie próximo e de baixa qualidade, não uma cópia nítida da foto de piscina.
- Manter o enquadramento idêntico ao original; não há zoom de câmera adicional a ser criado no PalmierPro.
- Preservar voz, pausa e textura de áudio original. A imagem termina no corte seco para o antes da cena 06.

## Critérios de aceite

- A identidade é plausivelmente a mesma mulher da cena 06: formato de rosto, pele, cabelo e idade aparente coerentes.
- Roupa verde-limão, crop muito fechado, iluminação interna e aparência de selfie comprimida preservados.
- Não transformar a selfie em retrato de estúdio; manter imperfeição e baixa qualidade coerentes com o original.
- Lábios acompanham “I made a New Year’s resolution.” sem alterar o áudio.
- Sem marca-d’água, legendas, ícone ou texto gerado.
- A cena encaixa em corte seco no antes da cena 06, sem salto visível de identidade.

## Estado

`aguardando aprovação humana do plano; nenhuma geração nova autorizada ainda`.
