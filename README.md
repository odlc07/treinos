# Treinos

Registro de corrida, natação e bike, com quilometragem automática dos tênis e das
bicicletas. Uma página estática no GitHub Pages — sem build, sem dependências, sem
servidor. No celular, vira app pelo "Adicionar à Tela de Início".

```
treinos/
├── index.html    o app inteiro
├── gear/         imagens dos equipamentos (WebP com fundo transparente)
│   ├── corre5.webp
│   └── rockhopper.webp
├── README.md
└── DEPLOY.md     passo a passo do deploy e da sincronização
```

Endereço depois do deploy: `https://odlc07.github.io/treinos/`

## As quatro telas

| | |
|---|---|
| **Painel** | tempo da semana, volume das últimas 8 semanas, totais e avisos de troca |
| **Treinos** | histórico por mês, filtrável por esporte; toque num treino para editar |
| **Equipamentos** | vitrine: foto grande de cada tênis e bike, com a quilometragem embaixo |
| **Detalhe** | toque num equipamento: foto, números e os treinos feitos com ele |
| **Ajustes** | metas semanais, sincronização, backup |

## Imagem do equipamento

Os arquivos ficam em `gear/`, e o cadastro guarda **só o nome** — o equipamento com
`media: "corre5"` carrega `gear/corre5.webp`. É de propósito: imagem não cabe no
`localStorage` (~5 MB no Safari, e base64 infla 33%) nem no Gist, então o binário mora
no repositório e a sincronização continua carregando apenas texto.

A contrapartida é que acrescentar imagem é tarefa de PC com `git push`, não de celular.

As imagens são recortadas com canal alfa, sem fundo. Isso não é capricho: foto de catálogo
tem fundo branco, e no tema escuro ela viraria um retângulo branco no meio da tela. Com
alfa, o objeto flutua sobre o card nos dois temas, e a sombra é feita em CSS
(`--fotoshadow`), que acompanha a silhueta e se ajusta ao tema.

Para acrescentar uma imagem:

1. Recorte o fundo (o iOS faz em Fotos, segurando o dedo no objeto → *Copiar Assunto*).
2. Salve como WebP em `gear/` — WebP com alfa pesa cerca de 1/5 do PNG equivalente.
3. Acrescente uma linha em `MEDIAS`, no topo do script:
   `{arq:'nomedoarquivo', nome:'Como aparece na lista'}`
4. `git push`, e o nome passa a aparecer no campo **Imagem** do equipamento.

O passo 3 existe porque hospedagem estática não permite listar um diretório: a página
não tem como descobrir sozinha o que há em `gear/`, então o índice é explícito. Um
equipamento que aponte para um arquivo fora da lista continua funcionando e aparece
marcado como *não listado* — editar outro campo não apaga a imagem sem você pedir.

### Quando virar 3D

A troca está isolada numa função só, `midiaHTML()`. Ela é o único lugar que decide o
que aparece no palco; o resto da tela não sabe a diferença. Trocar o `<img>` por um
`<model-viewer>` lendo o mesmo campo `media` não mexe em mais nada.

## Como a quilometragem é contada

Você não digita o km do tênis: ele é calculado.

```
km do equipamento = km que já tinha quando você cadastrou
                  + soma de todos os treinos marcados com ele
```

Por isso dá para corrigir o passado — editar ou apagar um treino recalcula o
equipamento na hora. O campo **Trocar em** é o aviso de fim de vida (tênis costumam
durar 600–800 km); vazio significa sem aviso, que é o normal para bicicleta.

Quando um tênis chega ao fim, marque **Aposentado** em vez de excluir: ele some das
listas de seleção, mas o histórico e o total continuam. Excluir desvincula os
treinos — eles continuam existindo, só ficam sem equipamento.

## Unidades

A distância é guardada **sempre em metros**, internamente. A tela converte para a
unidade de cada esporte na entrada e na saída:

| Esporte | Você digita | Ritmo mostrado |
|---|---|---|
| Corrida | km | min/km |
| Natação | m | min/100 m |
| Bike | km | km/h |

Isso existe para que 1.500 m de natação nunca tenham chance de entrar numa soma como
1.500 km. A duração é guardada em segundos e digitada em três campos (h / min / s) —
de propósito: num campo só, "1:30" é ambíguo entre 1h30 e 1min30.

## O que o painel mostra

- **Número herói:** tempo total da semana. Tempo é a única grandeza somável entre os
  três esportes — 1 km de natação não é comparável a 1 km de bike.
- **Comparação:** a semana corrente é confrontada com o **mesmo trecho** da semana
  anterior (segunda até o mesmo dia). Comparar uma quarta-feira com uma semana inteira
  faria o app anunciar uma queda de 70% toda quarta.
- **Volume:** colunas empilhadas, minutos por semana. Toque numa coluna para ler os
  números exatos dela.
- **Carga:** soma de `minutos × esforço percebido` da semana. Só aparece se você
  preencher o esforço.

## Onde os dados ficam

No `localStorage` do navegador — e, se você ligar a sincronização, também num Gist
secreto da sua conta do GitHub. O repositório guarda só o código: nada do seu treino
vai para lá. Atualizar o app não afeta os dados.

A sincronização **funde** os dois lados em vez de deixar o último sobrescrever: treinos
e equipamentos são unidos por `id`, exclusões viajam como lápides (para que apagar no
celular também apague no PC) e, em edição do mesmo item nos dois lados, vence a versão
mais recente. Lápides com mais de 180 dias são descartadas.

O backup manual em **Ajustes → Backup** vale mesmo com a sincronização ligada.
Importar **substitui** os dados atuais; a sincronização funde.

O passo a passo de tudo isso está no [DEPLOY.md](DEPLOY.md).
