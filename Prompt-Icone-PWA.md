# Prompt para criar o ícone PWA do seu site com IA

Duas etapas, cada uma na IA certa:

1. **O desenho** num **gerador de imagem** (Gemini, ou o gerador de imagem do ChatGPT). É ele que desenha bem.
2. **Os arquivos** (os tamanhos do PWA, o maskable, o favicon e o manifest) numa **IA que executa código** (Claude Code, ou o ChatGPT quando ele roda Python). Ela não desenha nada: só recorta, redimensiona e confere a imagem que você aprovou.

Por que separar: pedir tudo de uma vez faz a IA escolher um caminho só. Se ela escolhe desenhar por código, o ícone sai pobre. Se escolhe o gerador de imagem, os arquivos não saem. O PWA não precisa de SVG: o `manifest.json` usa PNG.

**Cores:** use as do seu site (as mesmas do logo e do tema). Os exemplos usam laranja e marrom só para mostrar o formato.

## 1. O desenho: prompt para o gerador de imagem

Cole no Gemini ou no gerador de imagem do ChatGPT. Peça 3 ou 4 variações e escolha uma.

```text
Crie o ícone de um aplicativo para celular. Só o símbolo, sem nenhum texto.

O aplicativo: [nome do app] — [o que ele faz, numa frase]
Símbolo: [ideia do desenho, ex.: "dois espetinhos cruzados sobre uma chama"; ou "sugira você"]

Estilo: [escolha UMA das duas linhas abaixo e apague a outra]
- REALISTA: ícone 3D realista de aplicativo premium, texturas reais, brilho, volume e profundidade,
  acabamento polido. O objeto flutua direto sobre o fundo: sem quadrado, placa, moldura ou ícone atrás dele.
- ILUSTRAÇÃO: ícone moderno de aplicativo, ilustração plana com volume leve, formas grandes e simples,
  contorno grosso na cor [cor do contorno, ex.: #2D1206], só as cores [#FF6B00], [#D64026], [#FFB703] e branco.
Um único objeto (ou grupo) principal, grande e bem recortado, com bastante contraste com o fundo,
reconhecível em tamanho pequeno (48 px).

Formato: imagem quadrada 1024 x 1024.
Fundo: uma cor só, lisa, [cor de fundo, ex.: #FF6B00], sem degradê e sem textura, preenchendo a imagem
inteira, de borda a borda, inclusive os quatro cantos (quadrado reto, sem cantos arredondados), sem moldura.
O símbolo centralizado, ocupando no máximo 60% da largura, com margem livre em volta
(o celular recorta as bordas do ícone).

Proibido: letras, palavras, números, logotipo de outra marca, mockup de celular, marca d'água,
mãos, pessoas, mesa ou cenário atrás, detalhes finos que somem em tamanho pequeno.
```

Se a imagem vier com texto, borda arredondada ou o símbolo grande demais, responda na mesma conversa: "refaça sem [o problema]; mantenha o resto".

Guarde a escolhida como `icone-1024.png`. Testado em 18/11/2026 com o Gemini, nos dois estilos: sai bom de primeira. Às vezes os cantos vêm levemente arredondados (a etapa 2 corrige); no realista, sem a frase "o objeto flutua direto sobre o fundo" ele desenha um ícone dentro do ícone.

## 2. Os arquivos: prompt para a IA que executa código

No **Claude Code** (na pasta do site) ou no **ChatGPT** (envie a imagem; ele precisa rodar Python). Envie o `icone-1024.png` junto.

```text
Tenho o ícone aprovado em icone-1024.png (quadrado, fundo sólido, símbolo no centro).
NÃO desenhe nada novo e não mude as cores: use só essa imagem.
Se os cantos vierem arredondados ou com borda clara, pinte cada canto com a cor do fundo logo ao lado dele.

Gere por código (Python com Pillow) e me entregue:
1. icon-512.png e icon-192.png: a imagem redimensionada.
2. icon-512-maskable.png: o símbolo inteiro dentro do círculo central de 80% da imagem.
   Se precisar, reduza a imagem e complete a borda com a própria imagem esticada e desfocada
   (assim não aparece emenda, mesmo se o fundo tiver variação de cor).
3. apple-touch-icon.png (180 x 180), sem transparência.
4. favicon.ico com os tamanhos 16, 32 e 48, e favicon-32.png.
5. O trecho "icons" do manifest.json apontando para esses arquivos
   (o maskable com "purpose": "maskable"; os outros com "purpose": "any").
6. As linhas do <head> do HTML: favicon e apple-touch-icon.

No fim, rode uma verificação e mostre uma tabela com o nome, as medidas em px e o tamanho em KB
de cada arquivo. Nenhum arquivo pode ter 0 bytes. Se estiver no Claude Code, coloque os arquivos
na pasta do site e atualize o manifest e o HTML.
```

## 3. Conferir antes de publicar

- [ ] O ícone não tem texto, e as cores são as do site.
- [ ] Em tamanho pequeno (abra o `icon-192.png` com zoom baixo ou no favicon da aba), o símbolo ainda se entende.
- [ ] No `icon-512-maskable.png`, nada importante encosta na borda: imagine um círculo de 80% no centro.
- [ ] A tabela da verificação tem todos os arquivos, com as medidas certas e nenhum com 0 KB.
- [ ] O `manifest.json` só aponta para arquivos que existem.
- [ ] Depois de publicar, instale o site no celular: o ícone na tela inicial não pode aparecer cortado.

Para instalar o site como aplicativo (PWA) com o Claude Code, veja [Site-Instalavel-PWA-com-Claude-Code.md](Site-Instalavel-PWA-com-Claude-Code.md).
