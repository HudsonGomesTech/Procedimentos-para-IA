# Prompt para pedir o ícone PWA a qualquer IA

Serve para ChatGPT, Gemini, Claude, Copilot etc. Copie o bloco, troque o que está entre `[colchetes]` e cole na IA. O que volta são dois SVG (normal e maskable). Os PNG nos tamanhos certos não são pedidos à IA de imagem: eles saem desses SVG (passo 3), sempre certos.

**Cores:** use as cores do seu site (as mesmas do logo e do tema). Os exemplos abaixo usam azul, verde e amarelo só para mostrar o formato: troque pelos códigos das suas cores.

## 1. Prompt para IA de texto (devolve o código SVG): o recomendado

```text
Crie o ícone de um aplicativo web (PWA) em SVG puro. Responda só com o código, sem explicação.

Aplicativo: [nome do app]
O que ele faz: [uma frase, ex.: "mostra as câmeras do portão para os vizinhos"]
Símbolo: [ideia do desenho, ex.: "um portão com um sinal de transmissão"; ou "sugira você"]

Regras do desenho:
- Um símbolo simples, que represente o que o app faz e seja reconhecível a 48 px.
- Proibido: texto ou palavras, só uma letra solta, emoji, foto, ícone genérico de biblioteca, sombra realista, detalhe fino.
- Traços e formas grossos; no máximo 3 cores além do fundo.
- Cores (use só estas): fundo [cor de fundo, ex.: #002776]; destaque [cor 2, ex.: #009C3B]; detalhe [cor 3, ex.: #FFDF00]; branco #FFFFFF.

Regras técnicas:
- <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" width="512" height="512">.
- Só formas vetoriais: rect, circle, path, polygon, line, linearGradient. Proibido: <text>, <image>, fontes,
  arquivos externos, <script>, <style> com @import, filtros (blur, drop-shadow), <foreignObject>.
- Todas as cores escritas direto nos atributos fill/stroke (hex).

Entregue DOIS arquivos, um bloco de código para cada:

1. icone.svg: fundo = retângulo 512x512 com cantos arredondados (rx="112"), na cor de fundo; o símbolo
   centralizado ocupando cerca de 70% da largura.

2. icone-maskable.svg: o MESMO desenho, mas com o fundo quadrado, SEM cantos arredondados (rect 512x512 sem rx),
   cobrindo tudo, e o símbolo reduzido para caber num círculo central de 80% (raio 205 px a partir do centro
   256,256): nada importante fora dessa área, porque o Android recorta as bordas.
```

## 2. Se a IA for só de imagem (gera PNG, não SVG)

Use quando a IA de texto não acertar o desenho. O PNG serve de rascunho: depois peça a uma IA de texto (ou ao Claude) para redesenhar em SVG com o prompt da seção 1, dizendo "baseado nesta imagem".

```text
Ícone de aplicativo, estilo flat, vetorial, minimalista, quadrado 1024x1024.
Símbolo central: [ideia do desenho] representando [o que o app faz].
Fundo sólido [cor de fundo, ex.: #002776] preenchendo a imagem inteira, sem cantos arredondados, sem moldura.
Símbolo em branco #FFFFFF com detalhes em [cor 2] e [cor 3], formas grossas, legível em tamanho pequeno,
ocupando no máximo 70% do centro, com margem livre em volta.
Sem texto, sem letras, sem sombra, sem degradê realista, sem efeito 3D, sem mockup de celular.
```

## 3. Conferir o que a IA devolveu

- [ ] São dois SVG com `viewBox="0 0 512 512"`; não há `<text>`, `<image>`, `<script>` nem link externo.
- [ ] `icone.svg` tem cantos arredondados; `icone-maskable.svg` tem fundo quadrado e o símbolo menor.
- [ ] As cores são só as pedidas.
- [ ] Abrir o SVG no navegador bem pequeno (zoom ou 48 px): o símbolo ainda se entende.
- [ ] Gerar os PNG: coloque os dois SVG no repositório do site e peça ao Claude Code: "gere a partir destes SVG os PNG icone-192.png, icone-512.png, icone-maskable-512.png e apple-touch-icon.png (180x180) e troque os ícones do site" (veja [Site-Instalavel-PWA-com-Claude-Code.md](Site-Instalavel-PWA-com-Claude-Code.md)). Olhar os PNG antes de publicar.
- [ ] Se algo veio errado, responda na mesma conversa: "refaça seguindo as regras técnicas; o problema foi: [...]".
