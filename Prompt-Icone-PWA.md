# Como pedir o ícone do seu PWA à IA

**O Pedido Perfeito: saiba como pedir à IA acertando de primeira.**

Um pedido pronto para você copiar, preencher cinco linhas e colar na IA. Ela devolve um `.zip` com todos os ícones
que um site instalável (PWA) precisa, mais o `manifest.json` e as tags do `<head>`.

> Testado em 22/11/2026 no Claude e no Gemini: os dois entregaram o `.zip` com os oito arquivos.
> A IA precisa conseguir gerar imagem e montar arquivos na mesma conversa.

## Como usar

1. Copie o pedido inteiro, do `=== PREENCHA AQUI ===` até o fim.
2. Preencha as cinco variáveis do topo. Não mude o resto.
3. Cole na IA e envie.
4. Ela mostra primeiro a imagem mestre (1024 x 1024). Se não gostou, peça o ajuste na mesma conversa; se gostou, responda "aprovado".
5. Baixe o `.zip` e coloque os arquivos na raiz do seu site.

## O pedido para copiar

```text
=== PREENCHA AQUI ===
NOME_DO_APP: 
O_QUE_O_APP_FAZ: 
OBJETO_CENTRAL: (ex.: um cofre, um cadeado, uma moeda)
COR_DO_FUNDO: 
COR_DO_OBJETO: (opcional; deixe "livre" para a IA escolher)
=====================

Crie um ícone de aplicativo para o meu PWA usando as variáveis acima.

ESTILO
- Um único objeto central: o OBJETO_CENTRAL, representando O_QUE_O_APP_FAZ.
- Visual 3D realista, com materiais, luz, sombras suaves e profundidade, no estilo dos ícones modernos de iOS/macOS.
- Nada de ícone flat, minimalista ou genérico.
- Nada de paisagem, cenário, pessoas ou vários elementos pequenos.
- Silhueta forte e alto contraste: precisa ser reconhecível em 48x48 pixels.
- Fundo simples em cor sólida ou degradê suave na COR_DO_FUNDO, preenchendo a imagem inteira até as bordas.
- Objeto na COR_DO_OBJETO, contrastando bem com o fundo.
- Sem texto, letras ou marca d'água.
- Objeto centralizado, ocupando cerca de 60% da imagem, com margem livre em volta.

PROCESSO
1. Se alguma variável acima estiver vazia, me pergunte antes de gerar.
2. Gere primeiro uma imagem mestre quadrada de 1024x1024 e me mostre para eu aprovar.
3. Depois de aprovada, redimensione a partir dela (não gere imagens novas para cada tamanho) e me entregue um ZIP com:
   - icon-192.png (192x192)
   - icon-512.png (512x512)
   - icon-maskable-512.png (512x512, objeto dentro dos 80% centrais, fundo até as bordas)
   - apple-touch-icon.png (180x180, sem transparência)
   - favicon-32.png (32x32) e favicon.ico
   - manifest.json com as entradas de ícones preenchidas e "name" igual ao NOME_DO_APP
   - snippet.html com as tags <link> para colocar no <head>
4. Todos os arquivos em PNG com cantos retos (o sistema operacional arredonda sozinho).
```

## Exemplo preenchido

```text
=== PREENCHA AQUI ===
NOME_DO_APP: Pão Dourado
O_QUE_O_APP_FAZ: aplicativo de uma padaria
OBJETO_CENTRAL: um pão
COR_DO_FUNDO: marrom claro
COR_DO_OBJETO: livre
=====================
```

## Por que cada regra existe

| Regra | O que ela evita |
|---|---|
| Variáveis no topo, separadas do resto | Você mexer nas regras sem querer ao trocar os dados. |
| "Se alguma variável estiver vazia, me pergunte" | A IA inventar o nome ou a cor por conta própria. |
| Um único objeto, sem cenário nem pessoas | Um desenho cheio de detalhes que vira um borrão no tamanho de ícone. |
| Reconhecível em 48x48 | Um ícone bonito grande e ilegível na tela do celular. |
| Fundo até as bordas, cantos retos | Borda branca ou canto duplo: o celular já arredonda o ícone sozinho. |
| Sem texto | Letras que ficam ilegíveis e erradas em imagem gerada por IA. |
| Objeto em cerca de 60%, com margem | O objeto cortado quando o Android recorta o ícone em círculo. |
| Imagem mestre aprovada antes | Receber um pacote inteiro de um desenho de que você não gostou. |
| "Redimensione a partir dela" | Cada tamanho sair com um desenho diferente. |
| Maskable com o objeto nos 80% centrais | O ícone cortado nos celulares que usam máscara. |
| `apple-touch-icon` sem transparência | Fundo preto atrás do ícone no iPhone. |

## O que vem no ZIP

| Arquivo | Medidas | Para que serve |
|---|---|---|
| `icon-192.png` | 192 x 192 | Ícone do app no Android e no manifest. |
| `icon-512.png` | 512 x 512 | Ícone grande e tela de abertura. |
| `icon-maskable-512.png` | 512 x 512 | Versão com folga, que o Android recorta em círculo ou outro formato. |
| `apple-touch-icon.png` | 180 x 180 | Ícone na tela inicial do iPhone e do iPad. |
| `favicon-32.png` e `favicon.ico` | 32 x 32 | Ícone da aba do navegador. |
| `manifest.json` | | Nome, cores e ícones do app instalável. |
| `snippet.html` | | Linhas para colar dentro do `<head>` do seu site. |

## Conferir antes de publicar

- [ ] O `.zip` tem os oito arquivos e nenhum está com 0 KB.
- [ ] O ícone não tem texto e o fundo vai até as bordas, com cantos retos.
- [ ] Olhando o `favicon-32.png`, o objeto ainda se entende.
- [ ] No `icon-maskable-512.png`, nada importante encosta na borda.
- [ ] O `"name"` do `manifest.json` é o nome do seu app, e ele só aponta para arquivos que existem.
- [ ] As linhas do `snippet.html` estão dentro do `<head>` do seu site.

Se algo veio errado, responda na mesma conversa: "refaça seguindo as regras; o problema foi: ...".

## Só os ícones não deixam o site instalável

Além do `manifest.json` e dos ícones, o navegador exige que o site esteja em **HTTPS**. Para funcionar offline,
o site precisa também de um service worker (`sw.js`), que este pedido não gera. O passo a passo está em
[Deixar o seu site instalável (PWA) com o Claude Code](Site-Instalavel-PWA-com-Claude-Code.md).

[Voltar à lista de procedimentos](README.md) do canal [Hudson Gomes Tech](https://www.youtube.com/@HudsonGomesTech).
