# Criar um site com IA: seguro, responsivo, animado e instalável no celular

Procedimento do canal [Hudson Gomes Tech](https://www.youtube.com/@HudsonGomesTech).

Um prompt pronto para você colar numa IA (Claude, ChatGPT, Gemini, Copilot) e receber um site completo:

- funciona bem no celular, com menu inferior e submenus;
- tem animações suaves, no estilo do [hudsongomestech.com.br](https://hudsongomestech.com.br);
- pode ser instalado na tela inicial do celular (PWA);
- já nasce com as proteções que eu uso nos meus sites;
- sai pronto para publicar no **Cloudflare Pages**: em um `.zip` (o padrão) ou gravado direto no seu repositório Git.

## Como usar

1. Copie o prompt da seção **O prompt**, inteiro.
2. Troque só o bloco **DADOS DO MEU SITE**, no começo. Ele já vem com dados de exemplo (uma padaria fictícia), para você
   ver o formato. Pode deixar campos em branco: a IA usa algo neutro no lugar.
3. Escolha a **ENTREGA** (último bloco dos dados):
   - **zip** (padrão): serve para qualquer IA, inclusive de chat (ChatGPT, Gemini). Você recebe um `.zip` e sobe no
     Cloudflare Pages arrastando o arquivo.
   - **git**: só para IA que roda comandos no seu computador ou servidor (Claude Code, por exemplo). Ela grava o site
     no seu repositório e o Cloudflare Pages publica sozinho a cada envio. Você informa o **endereço do repositório**
     e **onde está a chave** (o token do GitHub). A chave em si nunca vai no pedido: guarde antes com o procedimento
     [Claude Code editando o seu site no GitHub, sem vazar o token](Claude-Code-Editando-Site-no-GitHub.md).
4. Cole na IA. Numa IA de chat, se ela não conseguir gerar o `.zip`, ela mostra arquivo por arquivo: salve cada um
   com o nome que ela indicar.
5. Abra o `index.html` no navegador, confira no celular e peça ajustes na mesma conversa.
6. Publique no Cloudflare Pages (seção **Publicar no Cloudflare Pages**) e, enquanto estiver em construção ou se for
   área restrita, proteja com o **Cloudflare Access** (seção **Proteger com o Cloudflare Access**).

---

Se o logo que a IA desenhar não ficar bom, peça só o ícone com o
[prompt do ícone PWA](Prompt-Icone-PWA.md) e troque os arquivos da pasta `pwa/`.

## O prompt

```text
=== DADOS DO MEU SITE (troque pelos seus) ===
NOME: Padaria Pão Dourado
OBJETIVO: mostrar os produtos, o horário de funcionamento e receber pedidos de encomenda pelo WhatsApp
TEMA / ESTILO: aconchegante e artesanal, com cara de padaria de bairro; textos simples e próximos
CORES: marrom café #4A2C1A, dourado #E0A526, creme #FFF6E5 (fundo) e um verde-oliva #6B7F3A para destaques
PÚBLICO: moradores do bairro, famílias, pessoas que encomendam bolo para festa
PÁGINAS / SEÇÕES: Início, Produtos (Pães, Bolos, Salgados), Encomendas, Sobre nós, Contato
CONTATOS: WhatsApp (11) 90000-0000 · e-mail contato@paodourado.exemplo · Instagram @paodourado.exemplo
ENDEREÇO: Rua das Flores, 123, Centro, Cidade Exemplo - SP · seg a sáb 6h às 20h, dom 6h às 12h
DETALHES EXTRAS: destacar o bolo de fubá da casa; botão fixo "Pedir pelo WhatsApp"; mostrar o mapa do endereço

PUBLICAÇÃO: Cloudflare Pages
ENTREGA: zip
  (troque por "git" só se a IA roda comandos no seu computador; aí preencha as duas linhas abaixo)
REPOSITÓRIO GIT: https://github.com/seu-usuario/seu-repositorio.git
CHAVE DO GIT: guardada no arquivo /etc/credentials/git-token.env, variável GITHUB_TOKEN
  (aqui vai só ONDE a chave está; nunca cole o token neste pedido)
=== FIM DOS DADOS ===

Você é um desenvolvedor web sênior, cuidadoso com segurança. Crie um site completo com os DADOS acima, seguindo
TODAS as regras abaixo. Se algum dado estiver em branco, use algo neutro e me avise no final. Não invente
preços, prêmios ou depoimentos: onde faltar, deixe um texto marcado como [PREENCHER].

1. TECNOLOGIA E ARQUIVOS
- HTML, CSS e JavaScript puros, sem framework e sem etapa de build. Arquivos separados:
  index.html (e outras páginas se precisar), css/estilo.css, js/script.js, manifest.webmanifest, sw.js,
  offline.html, pwa/ (ícones), _headers.
- Nada de CSS ou JavaScript dentro do HTML (sem <style>, sem atributo style="...", sem <script> com código,
  sem onclick="..."). Isso vale também para os SVG dentro do HTML: use fill/stroke, não style.
  Isso é necessário para a política de segurança (CSP) do item 5.
- Não use bibliotecas de CDN de terceiros. Se for indispensável, use versão fixa e atributo integrity (SRI).
- Textos em português do Brasil, com acentuação correta. <html lang="pt-BR">.

2. RESPONSIVO, PENSADO PRIMEIRO PARA O CELULAR
- Mobile-first: comece pelo layout de celular (360 px de largura) e amplie com media queries para tablet e computador.
- Nada pode passar da largura da tela (sem rolagem para o lado). Imagens com max-width: 100%.
- Botões e links com área de toque de pelo menos 44 x 44 px; texto base de 16 px no mínimo.
- Respeite a área segura de iPhone com notch: padding com env(safe-area-inset-bottom) no que fica colado embaixo.
- Teste mentalmente em 360 px, 768 px e 1280 px e ajuste o que quebrar.

3. MENU
- No CELULAR: barra de menu INFERIOR fixa, com 4 ou 5 itens (ícone + nome curto), o item da página atual destacado.
  Se uma seção tiver subpáginas (ex.: Produtos > Pães, Bolos, Salgados), tocar nela abre um SUBMENU em painel que
  sobe de baixo (bottom sheet), com fundo escurecido; fecha ao tocar fora, no X ou com a tecla Esc.
  Se houver mais de 5 seções, o último item é "Mais" e abre o painel com o resto.
- No COMPUTADOR: menu no topo, com os submenus abrindo em lista ao passar o mouse e também ao clicar ou usar o teclado.
- Acessível: <nav aria-label="...">, aria-expanded nos itens com submenu, foco visível, navegação completa pelo teclado.
- Ícones do menu em SVG desenhados no próprio código (nada de emoji como ícone).

4. ANIMAÇÕES (site vivo, mas leve)
- Elementos surgem suavemente ao rolar a página (subir uns 20 px e aparecer), usando IntersectionObserver;
  cada item de uma lista entra com um pequeno atraso em relação ao anterior.
- Cabeçalho com animação de entrada na abertura; cartões e botões reagem ao passar o mouse ou tocar
  (leve elevação, sombra, mudança de cor). Transições de 0,2 s a 0,8 s com curva suave (ease-out).
- Rolagem suave para as âncoras. Se fizer sentido, um detalhe animado no fundo do topo (formas ou degradê em movimento lento).
- Anime só transform e opacity (não anime largura, altura ou margens: trava no celular).
- OBRIGATÓRIO: respeitar @media (prefers-reduced-motion: reduce), desligando as animações para quem pediu menos
  movimento no aparelho. O conteúdo precisa aparecer mesmo se o JavaScript falhar.

5. SEGURANÇA
- Nenhum segredo no código: nada de senha, token, chave de API ou e-mail de login dentro dos arquivos.
  Se algo precisar de chave, deixe indicado onde ela vai (variável de ambiente do servidor), nunca o valor.
- Crie o arquivo _headers (formato do Cloudflare Pages) com estes cabeçalhos para todas as páginas:
  Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self'; img-src 'self' data:;
    font-src 'self'; connect-src 'self'; manifest-src 'self'; worker-src 'self'; frame-src https://www.google.com;
    base-uri 'self'; form-action 'self'; frame-ancestors 'none'; object-src 'none'
  (frame-src só se houver mapa do Google; ajuste a lista se usar outro serviço e me diga o que liberou e por quê)
  Strict-Transport-Security: max-age=31536000; includeSubDomains
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Permissions-Policy: camera=(), microphone=(), geolocation=(), payment=()
  e Cache-Control: no-cache para as páginas HTML.
- Links para outros sites com target="_blank" sempre com rel="noopener noreferrer".
- Formulários: validação no navegador (required, type="email", maxlength) e aviso claro de que a validação de
  verdade precisa acontecer no servidor. Não monte HTML com innerHTML a partir de algo digitado pelo usuário:
  use textContent.
- Contato por WhatsApp com link https://wa.me/<número> e mensagem pronta; e-mail com mailto:. Não exponha
  nenhum dado pessoal além dos que estão nos DADOS.
- Mapa: use o iframe de incorporação do Google Maps com loading="lazy" e referrerpolicy, e um link
  "Abrir no mapa" para quem bloqueia iframes.

6. PWA (instalável na tela inicial)
- Desenhe um LOGO em SVG para este site (símbolo simples do que ele faz, nas CORES dos dados, sem texto, legível
  a 48 px). Entregue pwa/icone.svg (512x512, cantos arredondados) e pwa/icone-maskable.svg (fundo quadrado sem
  cantos, símbolo dentro do círculo central de 80%). Explique como gerar os PNG 192, 512, 512 maskable e o
  apple-touch-icon 180 a partir deles (ex.: abrir o SVG no navegador e exportar, ou um site de conversão).
- manifest.webmanifest com name, short_name (até 12 letras), description, lang "pt-BR", start_url "/", scope "/",
  display "standalone", background_color, theme_color e os ícones (192, 512 e 512 com purpose "maskable").
- No <head> de toda página: link rel="manifest", meta theme-color, link rel="icon" (o SVG), link rel="apple-touch-icon".
- sw.js MÍNIMO: guarda em cache só os arquivos estáticos (CSS, JS, ícones) e a página offline.html; quando não
  houver internet, a navegação mostra offline.html. Nome do cache com versão (ex.: "site-v1") e limpeza dos caches
  antigos na ativação. Nunca guardar respostas de formulário, API ou dados de usuário. Registre o service worker
  pelo js/script.js.
- Nos links de CSS, JS e imagens use ?v=1 no fim (ex.: css/estilo.css?v=1) e me explique que esse número deve mudar
  a cada atualização, para o visitante não ficar com a versão velha. A lista de arquivos do sw.js usa os mesmos
  endereços com ?v=, e a versão do cache (ex.: "site-v1" → "site-v2") sobe junto.

7. QUALIDADE
- HTML semântico (header, nav, main, section, footer), um único <h1> por página, títulos em ordem.
- Contraste de cor suficiente (WCAG AA) entre texto e fundo; teste as CORES e ajuste o tom se não passar.
- Todas as imagens com alt descritivo, width e height, e loading="lazy" (menos a primeira da página).
- <title> e <meta name="description"> próprios em cada página; meta viewport; tags Open Graph (título, descrição,
  imagem) para o link ficar bonito no WhatsApp.
- Use imagens de exemplo leves (SVG ou blocos de cor) e me diga onde trocar pelas fotos reais.
- Rodapé com nome, endereço, contatos, horário e o ano atual.

8. ENTREGA (sempre para o Cloudflare Pages)
- O site é publicado no Cloudflare Pages, sem etapa de build: index.html na RAIZ (nunca dentro de uma subpasta),
  _headers na raiz, e nada que dependa de servidor próprio.
- Se ENTREGA = zip (ou em branco): gere UM arquivo .zip com o index.html na raiz e me dê o link para baixar.
  Se não conseguir gerar o .zip, me entregue cada arquivo completo, com o caminho (ex.: css/estilo.css), sem
  trechos cortados ou "...".
- Se ENTREGA = git: grave os arquivos na raiz do REPOSITÓRIO GIT informado, no branch main, faça o commit com uma
  mensagem em português e envie (push). Use a chave do lugar indicado em CHAVE DO GIT, seguindo estas regras:
  nunca mostre o valor da chave na tela (nada de cat, echo ou comando cuja saída a contenha); não a grave em
  nenhum arquivo, nem no endereço do repositório, nem na mensagem de commit; se a chave não estiver lá ou o envio
  falhar, pare e me avise, sem pedir que eu cole a chave na conversa. Se a sua ferramenta não consegue rodar
  comandos, diga isso e entregue em .zip.
- No final: a árvore de pastas, como testar no computador e no celular, o que eu preciso trocar (lista dos
  [PREENCHER]) e uma lista do que foi feito para segurança.
```

---

## Publicar no Cloudflare Pages

É gratuito. Os nomes no painel mudam de vez em quando; o caminho é parecido com este:

**Se a entrega foi zip:**

1. Painel da Cloudflare → **Workers e Pages** → **Criar** → aba **Pages** → **Enviar ativos** (Upload assets).
2. Dê um nome ao projeto (ele vira o endereço `nome.pages.dev`) e arraste o `.zip`.
3. **Implantar**. Para atualizar depois, abra o projeto → **Criar nova implantação** e arraste o `.zip` novo.

**Se a entrega foi git:**

1. Painel da Cloudflare → **Workers e Pages** → **Criar** → aba **Pages** → **Conectar ao Git**.
2. Autorize o GitHub e escolha o repositório.
3. Predefinição de estrutura: **Nenhuma**; comando de build: vazio; diretório de saída: `/`.
4. **Salvar e implantar**. A partir daí, cada envio (push) para o branch `main` publica o site sozinho.

Um projeto criado por zip não vira projeto Git depois. Se pensa em passar a usar Git, já comece por ele.

Para usar seu domínio (ex.: `meusite.com.br`): no projeto, **Domínios personalizados** → **Configurar domínio**.

## Proteger com o Cloudflare Access

O site pode ficar fechado para o público enquanto você termina, ou ter uma área só para você e sua equipe. O
**Cloudflare Access** coloca uma tela de login na frente do site, antes mesmo de ele carregar: quem não está na lista
não vê nada. Ele é gratuito para até 50 pessoas.

Os nomes no painel da Cloudflare mudam de vez em quando; o caminho é parecido com este:

1. Painel da Cloudflare → **Zero Trust** (na primeira vez, ele pede um nome de equipe e o plano Free; pode pedir
   um cartão, mas o plano Free não cobra).
2. **Access** → **Applications** → **Add an application** → **Self-hosted**.
3. Nome do aplicativo e o endereço do site (ex.: `meusite.com.br`, ou só `meusite.com.br/admin` para proteger uma área).
4. **Policy**: ação **Allow**, regra **Emails**, com os e-mails que podem entrar (ou **Emails ending in**
   `@suaempresa.com.br`).
5. Login: **One-time PIN**. A pessoa digita o e-mail e recebe um código; não há senha para vazar.
6. Salve e teste numa janela anônima: tem que aparecer a tela da Cloudflare pedindo o e-mail.

**Atenção ao endereço `.pages.dev`:** o Cloudflare Pages também publica o site em `seu-projeto.pages.dev`. Proteger
só o seu domínio deixa esse outro endereço aberto. Crie a mesma regra no Access para `seu-projeto.pages.dev` (no
projeto do Pages, em **Settings**, há a opção de ativar o Access para as prévias, `*.seu-projeto.pages.dev`).

Quando o site estiver pronto para todo mundo, é só apagar o aplicativo no Access (ou deixar só na área `/admin`).

---

## Dicas

- **Peça por partes se a IA cortar a resposta:** "agora só o css/estilo.css completo", "agora o js/script.js".
- **Ajustes:** descreva o que viu e em que tela. Ex.: "no celular de 360 px o menu inferior cobre o botão do
  WhatsApp; suba o botão acima do menu".
- **Teste no celular de verdade:** no Android, o menu do Chrome tem que oferecer **Instalar app** com o seu logo. No
  iPhone, a instalação é pelo Safari: **Compartilhar** → **Adicionar à Tela de Início**.
- **Teste sem internet:** com o site já aberto uma vez, ligue o modo avião e recarregue: tem que aparecer a página
  `offline.html`, não a tela de erro do navegador.
- **Nota de qualidade:** no Chrome do computador, aperte F12 → aba **Lighthouse** → analise a página. Ele aponta
  problemas de desempenho, acessibilidade e boas práticas, e você pode colar o resultado na IA para ela corrigir.
- **Algo do site parou de carregar depois de publicar?** Quase sempre é a política de segurança (CSP) bloqueando um
  serviço de fora (vídeo do YouTube, fonte, formulário externo). Aperte F12 → **Console**, copie a mensagem de erro
  e peça à IA para liberar só aquele endereço no `_headers`.
- **HSTS com `includeSubDomains`:** obriga HTTPS em todos os subdomínios do seu domínio por um ano. Se algum
  subdomínio ainda não tem HTTPS, tire o `includeSubDomains` antes de publicar.
- **Teste de segurança rápido:** depois de publicar, coloque o endereço em https://securityheaders.com e veja a nota.
- **Nunca cole senha, token ou chave na conversa com a IA.** Se precisar de uma, diga só o nome da variável.
- **Revise antes de publicar:** a IA pode errar telefone, horário ou endereço. Confira todos os dados.

---

Licença [MIT](LICENSE). Use, adapte e compartilhe à vontade.
