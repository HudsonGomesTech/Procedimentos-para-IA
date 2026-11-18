# Deixar o seu site instalável (PWA) com o Claude Code

Procedimento do canal [Hudson Gomes Tech](https://www.youtube.com/@HudsonGomesTech).

A ideia: o site que você já tem no ar vira um "aplicativo". No computador aparece o botão **Instalar** na barra do
navegador; no celular, a opção **Instalar app** (ou "Adicionar à tela inicial"). Depois de instalado, ele abre em
janela própria, sem a barra de endereço, com o ícone do seu site. Sem loja de aplicativo e sem pagar nada.

Isso se chama PWA. Precisa de três coisas no site: um **manifesto** (nome, cores e ícones do app), os **ícones** em
tamanhos certos e um arquivo pequeno chamado **service worker**. O Claude Code cria as três a partir de um pedido.

## O que você precisa

- Um site já publicado com endereço `https://` (por exemplo no Cloudflare Pages, ligado a um repositório do GitHub).
  PWA não funciona em endereço sem certificado SSL.
- O Claude Code com acesso ao repositório do site (veja o procedimento
  [Claude-Code-Editando-Site-no-GitHub.md](Claude-Code-Editando-Site-no-GitHub.md)).
- O logo do site, de preferência em SVG ou em PNG grande (512 x 512 ou mais), dentro do repositório.

## 1. O pedido

Abra o Claude Code na pasta do repositório do site, copie, troque o que está entre `[ ]` e cole:

```text
Quero deixar o meu site instalável como aplicativo (PWA), no computador e no celular.

Nome do app: [nome completo do site]
Nome curto (aparece embaixo do ícone, até 12 letras): [nome curto]
Cor principal: [ex.: #0A0F1A] (use a cor de fundo do site se eu não souber)
Logo: [caminho do logo no repositório, ex.: img/logo.svg]

Faça, nesta ordem:
1. Gere os ícones a partir do logo: 192x192, 512x512, um 512x512 "maskable" (com margem para o Android
   recortar em círculo) e o apple-touch-icon de 180x180. Mostre como ficaram antes de seguir.
2. Crie o manifest.webmanifest com name, short_name, start_url, scope, display "standalone",
   background_color, theme_color, lang "pt-BR" e os ícones acima.
3. Crie um service worker mínimo que guarde só os arquivos estáticos (CSS, JS, imagens) e uma página
   "sem conexão". Nunca guarde páginas com login, respostas de API ou dados do usuário.
4. Ligue tudo em todas as páginas: <link rel="manifest">, <meta name="theme-color">, o apple-touch-icon
   e o registro do service worker.
5. Se o site tem política de segurança (CSP), libere manifest-src 'self' e worker-src 'self'.
6. Confira com o Lighthouse ou com as ferramentas do navegador se o site ficou instalável e me diga o resultado.
7. Faça um commit só, com mensagem clara, e envie para o GitHub.
```

Se o site é publicado pelo Cloudflare Pages ligado ao GitHub, a publicação começa sozinha depois do envio.

## 2. Conferir no computador

1. Abra o site e recarregue sem cache: **Ctrl + F5**.
2. Na barra de endereço aparece o ícone **Instalar**. Clique e confirme.
3. O site abre em janela própria, sem a barra do navegador, e ganha atalho na área de trabalho e no menu Iniciar.

## 3. Conferir no celular (Android, Chrome)

1. Abra o site no navegador.
2. Toque nos **três pontinhos**.
3. Toque em **Instalar app** (em inglês: *Install*). Em alguns celulares aparece "Adicionar à tela inicial".
4. Confirme. O ícone aparece na tela inicial.
5. Toque no ícone: o site abre em tela cheia, sem a barra de endereço.

No iPhone (Safari), o caminho é o botão **Compartilhar** e depois **Adicionar à Tela de Início**.

## Se der diferente

- **O botão Instalar não aparece:** espere a publicação terminar, recarregue com Ctrl + F5 e confira se o endereço é
  `https://`. Peça ao Claude Code: "o botão de instalar não aparece; confira o manifesto e o service worker com as
  ferramentas do navegador e corrija".
- **O ícone ficou cortado no Android:** o ícone maskable está sem margem. Peça para refazer o maskable com o desenho
  dentro do círculo central de 80%.
- **Mudei o site e o app instalado mostra a versão antiga:** feche e abra o app de novo. Se continuar, peça ao Claude
  Code para mudar a versão do cache do service worker.

## Ícone novo feito por IA

Se o seu logo não serve como ícone (muito detalhe, texto pequeno), use o
[Prompt-Icone-PWA.md](Prompt-Icone-PWA.md) para pedir um ícone a qualquer IA e depois peça ao Claude Code para
trocar os ícones do site por ele.
