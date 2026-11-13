# Criando sites com o ChatGPT gratuito

Escopo pronto para pedir um site ao ChatGPT (plano gratuito) e publicar de graça no Cloudflare Pages.
Você copia o texto abaixo, troca o que está entre colchetes e recebe um arquivo `.zip` pronto para subir.

> Procedimento validado em 13/11/2026, com o ChatGPT no plano gratuito e o painel da Cloudflare dessa data.
> As telas mudam com o tempo: se algum botão estiver diferente, a ideia do passo continua a mesma.

## Como usar

1. Copie o escopo inteiro.
2. Troque tudo o que está entre `[colchetes]` pelos dados do seu site.
3. Cole no ChatGPT e envie.
4. Baixe o `.zip` que ele gerar e suba no Cloudflare Pages (passo a passo no fim).

## Escopo para copiar

```text
Crie um site completo para mim e entregue em um único arquivo .zip para download.

SOBRE O SITE
- Nome: [nome do site ou do negócio]
- Assunto: [o que é, em uma ou duas frases]
- Público: [para quem é o site]
- Objetivo: [o que o visitante deve fazer: entrar em contato, conhecer o serviço, etc.]
- Páginas ou seções: [exemplo: Início, Sobre, Serviços, Contato]
- Estilo visual: [exemplo: moderno, fundo escuro, azul e branco]
- Contato que deve aparecer: [e-mail, WhatsApp ou redes sociais]
- Textos: [cole seus textos aqui ou escreva "crie os textos para mim"]

REGRAS TÉCNICAS (não mude)
1. Site estático: só HTML, CSS e JavaScript puro. Sem framework, sem etapa de build, sem servidor e sem banco de dados.
2. O arquivo index.html fica na raiz do .zip, nunca dentro de uma subpasta.
3. Estrutura: index.html, 404.html, pasta css/, pasta js/ e pasta img/.
4. Todos os caminhos são relativos (exemplo: css/estilo.css). Nada de caminho começando com C:\ ou com /.
5. Não dependa de arquivos de outros sites: sem CDN e sem bibliotecas externas. Use fontes do sistema.
6. Imagens: crie ilustrações e ícones em SVG dentro do próprio site. Não use fotos de terceiros nem links de imagem.
7. O site deve funcionar bem no celular, no tablet e no computador.
8. Idioma português do Brasil, com lang="pt-BR", título e descrição em cada página.
9. Inclua um favicon em SVG.
10. Formulário de contato não funciona em site estático: use botão de WhatsApp ou link de e-mail.
11. Código organizado e comentado em português.

ENTREGA
- Gere o arquivo .zip e me dê o link para baixar.
- Depois do link, liste os arquivos que estão dentro do .zip.
- Se não conseguir gerar o .zip, mostre cada arquivo completo em um bloco de código separado,
  com o nome do arquivo em cima, para eu copiar um por um.
```

## Exemplo preenchido

```text
- Nome: Hudson Gomes Tech
- Assunto: canal de tecnologia que ensina a publicar sites e servidores de casa de forma simples
- Público: iniciantes em tecnologia
- Objetivo: levar o visitante a se inscrever no canal
- Páginas ou seções: Início, Sobre o canal, Vídeos, Contato
- Estilo visual: moderno, fundo azul bem escuro, detalhes em ciano, letras claras
- Contato que deve aparecer: link do canal no YouTube
- Textos: crie os textos para mim
```

## Pedidos de ajuste

Depois que o site vier, peça mudanças na mesma conversa e termine sempre com "gere o .zip atualizado":

- `Troque a cor principal para verde e gere o .zip atualizado.`
- `Adicione uma seção de perguntas frequentes com 5 perguntas e gere o .zip atualizado.`
- `Deixe o título da página inicial maior no celular e gere o .zip atualizado.`

## Confira antes de publicar

- Abra o `.zip`: o `index.html` aparece logo na primeira tela, fora de qualquer pasta.
- Extraia em uma pasta e dê dois cliques no `index.html`: o site abre no navegador com cores e imagens.
- Clique em todos os links do menu.

## Publicando no Cloudflare Pages

1. Entre em [dash.cloudflare.com](https://dash.cloudflare.com) com uma conta gratuita.
2. No menu, abra **Compute** → **Workers & Pages** e clique em **Create application**.
3. Atenção: a tela abre no modo Worker. Não escolha nenhuma das opções da caixa.
   Clique no link pequeno embaixo dela: **Continue to Pages**.
4. Em **Drag and drop your files**, clique em **Get started**.
5. Dê um nome ao projeto, só com letras minúsculas, números e hífen (exemplo: `meu-site`).
   Ele vira o endereço `meu-site.pages.dev`. Clique em **Create project**.
6. Arraste o `.zip` para a área de envio e espere aparecer "All files were successfully uploaded".
7. Clique em **Deploy site** e abra o endereço `pages.dev` que aparece na tela de sucesso.

Para atualizar o site, entre no projeto e clique em **Create deployment**.

## Problemas comuns

| Problema | Solução |
|---|---|
| O ChatGPT não gerou o `.zip` | Peça: `Gere o .zip agora`. Se não der, peça os arquivos em blocos de código e monte a pasta você mesmo. |
| O site abre sem cor ou sem imagem | Algum caminho está errado. Peça: `Confira se todos os caminhos são relativos e gere o .zip atualizado.` |
| Não acho onde enviar o `.zip` | A tela de criação abre no modo Worker. Clique em **Continue to Pages**, no rodapé da caixa. |
| O nome do projeto dá erro | Use só letras minúsculas, números e hífen, sem espaço e sem maiúscula. |
| Página em branco no Cloudflare | O `index.html` ficou dentro de uma subpasta. Suba a pasta que contém o `index.html` diretamente. |
| Limite do plano gratuito atingido | Espere o limite renovar ou continue em uma conversa nova colando o escopo e o que falta. |

---

Material do canal **Hudson Gomes Tech**. Uso livre, licença MIT. Validado em 13/11/2026.
