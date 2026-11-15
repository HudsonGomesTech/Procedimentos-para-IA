# Chat de contato no site, com as mensagens chegando no seu e-mail

Procedimento do canal [Hudson Gomes Tech](https://www.youtube.com/@HudsonGomesTech).

A ideia: o visitante escreve uma mensagem em uma caixa de chat no site, e ela chega no seu e-mail. Quem faz o trabalho é o Claude Code: você pede em português, ele escreve o código e envia ao GitHub. O site se atualiza sozinho pelo Cloudflare Pages.

Neste exemplo:

| O quê | Valor usado no canal | Troque pelo seu |
|---|---|---|
| Repositório do site | `https://github.com/HudsonGomesTech/site-pronto-gpt.git` | o repositório do seu site |
| Remetente (quem envia) | `site@hudsongomestech.com.br` | `site@seu-dominio` |
| Destinatário (quem recebe) | `contato@hudsongomestech.com.br` | `contato@seu-dominio` |

O cuidado principal é com o token da Cloudflare: ele fica guardado como **variável secreta na Cloudflare**. Não vai para o código, não vai para o GitHub e não é colado na conversa com o Claude.

## Como funciona

```text
visitante escreve no chat
        ↓
página do site (HTML, CSS e JS no GitHub)
        ↓  envia a mensagem para /api/contato
função do Cloudflare Pages (arquivo functions/api/contato.js)
        ↓  lê o token da variável secreta e chama a API de e-mail
Cloudflare Email Sending
        ↓
caixa contato@seu-dominio
```

O navegador do visitante nunca vê o token. Só a função, que roda dentro da Cloudflare, tem acesso a ele.

## O que você precisa

- O site no **Cloudflare Pages**, ligado a um repositório do **GitHub**.
- O domínio na Cloudflare, com o endereço `contato@seu-dominio` já recebendo e-mail (Email Routing).
- O **Email Sending** da Cloudflare ativado para o domínio. Na data deste procedimento (novembro de 2026) ele está em beta e exige o plano pago Workers, de 5 dólares por mês.
- O **Claude Code** instalado no servidor, com o token do GitHub guardado em `/etc/credentials/git-token.env`, na variável `GITHUB_TOKEN`. Isso está no procedimento [Claude Code editando o seu site no GitHub](Claude-Code-Editando-Site-no-GitHub.md).

## 1. Criar o token de envio de e-mail

1. No painel da Cloudflare: **Email Service**, **Email Sending**, clique no seu domínio.
2. Abra **Connect to Email Service** e escolha a aba **REST API**.
3. Clique em **Create Email Sending token**.
4. Dê um nome que diga para que serve, por exemplo `site-contato`. A permissão já vem certa: **Email Sending**, nível **Edit**, só para a sua conta.
5. Clique em **Create API Token** e copie o token. Ele aparece uma única vez.

Na mesma tela, o exemplo de envio mostra um endereço parecido com este:

```text
https://api.cloudflare.com/client/v4/accounts/SEU_ID_DA_CONTA/email/sending/send
```

O trecho entre `accounts/` e `/email` é o **ID da sua conta**. Copie também, você vai usar no passo 2.

Não cole o token em conversa, e-mail, anotação ou captura de tela. Ele vai direto para o passo 2.

## 2. Guardar o token como variável secreta na Cloudflare

1. No painel: **Workers & Pages**, clique no projeto do seu site.
2. **Settings**, **Variables and Secrets**, **Add**.
3. Crie as duas variáveis abaixo, no ambiente **Production**:

| Nome | Tipo | Valor |
|---|---|---|
| `CLOUDFLARE_API_TOKEN` | **Secret** | o token do passo 1 |
| `CLOUDFLARE_ACCOUNT_ID` | **Secret** | o ID da conta do passo 1 |

4. Salve.

O tipo **Secret** esconde o valor: depois de salvo, nem você consegue ler de novo no painel. É isso que queremos.

As variáveis só passam a valer na próxima publicação do site. A publicação acontece sozinha quando o Claude Code enviar o código, no passo 3.

## 3. O pedido

No servidor, entre na pasta de trabalho e abra o Claude Code:

```bash
cd ~/site
claude
```

Copie o pedido abaixo, troque o que está entre `< >` e cole:

```text
Você será o meu editor de site.

O site está no GitHub, no repositório <https://github.com/HudsonGomesTech/site-pronto-gpt.git>,
e é publicado pelo Cloudflare Pages a cada envio para o ramo principal.

Já temos neste servidor um token do GitHub com permissão de escrita nesse repositório.
Ele está no arquivo /etc/credentials/git-token.env, na variável GITHUB_TOKEN.

Regras sobre o token do GitHub:
- Nunca mostre o valor dele na tela: nada de cat, echo, grep ou qualquer comando cuja saída contenha o token.
- Carregue o arquivo para dentro de uma variável e use só a variável.
- Não grave o token em nenhum outro arquivo, nem no endereço do repositório, nem em mensagem de commit.
- Se por algum motivo o token aparecer na tela, pare e me avise.

O que eu quero:
Adicione ao site um chat de contato, para o visitante enviar uma mensagem para nós.
- Um botão flutuante no canto da página abre uma caixa de chat, no mesmo visual do site.
- A caixa pede o nome, o e-mail do visitante e a mensagem, e tem um botão Enviar.
- Depois de enviar, a caixa mostra uma confirmação. Se falhar, mostra um aviso simples e deixa tentar de novo.
- A mensagem deve chegar por e-mail, enviada pela API de Email Sending da Cloudflare:
  remetente: <site@hudsongomestech.com.br>
  destinatário: <contato@hudsongomestech.com.br>
  assunto: "Mensagem pelo site", seguido do nome do visitante.
  corpo: o nome, o e-mail do visitante e a mensagem.

Como fazer o envio:
- Crie uma função do Cloudflare Pages no arquivo functions/api/contato.js, que aceita só POST.
- A página envia os dados para /api/contato. O navegador nunca fala direto com a API da Cloudflare.
- A função chama:
  POST https://api.cloudflare.com/client/v4/accounts/<ID da conta>/email/sending/send
  cabeçalhos: Authorization: Bearer <token> e Content-Type: application/json
  corpo em JSON com os campos to, from, subject, text e html.
- O token e o ID da conta já estão guardados no projeto do Pages, como variáveis secretas:
  CLOUDFLARE_API_TOKEN e CLOUDFLARE_ACCOUNT_ID. Na função, leia de context.env.
- Não invente campos da API. Se precisar de algo além desses, consulte a documentação oficial da Cloudflare.

Regras de segurança do chat:
- O token e o ID da conta nunca aparecem no código, no repositório, nas respostas da função nem em logs.
  Você não precisa deles: não me peça, e não tente ler.
- Remetente e destinatário são fixos no código da função. Nada que venha do formulário muda quem envia ou quem recebe.
- Valide na função, e não só na página: nome até 80 caracteres, e-mail com formato válido e até 120,
  mensagem de 5 a 2000 caracteres. Fora disso, responda com erro e não envie.
- Antes de montar o corpo em HTML, escape o texto do visitante.
- Inclua um campo oculto de armadilha contra robôs: se vier preenchido, responda como se tivesse enviado, e não envie.
- Aceite só pedidos vindos do próprio site (confira o cabeçalho Origin).
- Em caso de erro, mostre ao visitante só uma mensagem genérica. Detalhes da API não vão para a página.

Como trabalhar:
- Baixe o repositório para esta pasta, leia os arquivos e só então altere.
- Mantenha o padrão do site: HTML no index.html, estilo no arquivo de CSS e comportamento no arquivo de JS que já existem.
- Antes de enviar, me diga em poucas linhas o que mudou e em quais arquivos.
- Envie tudo em um único commit, com uma mensagem que explique a mudança.
- No fim, me diga o endereço do commit e como eu testo.
```

## 4. Conferir

1. No painel da Cloudflare, em **Workers & Pages**, abra o projeto e espere a publicação nova terminar. Leva um ou dois minutos.
2. Abra o site, clique no botão do chat e envie uma mensagem de teste.
3. Abra a caixa `contato@seu-dominio`. A mensagem deve chegar em instantes.
4. Em **Email Service**, **Email Sending**, o painel mostra o envio contado.

## Se não funcionar

| O que acontece | O que conferir |
|---|---|
| O chat mostra erro ao enviar | As duas variáveis existem no ambiente **Production**, com os nomes exatos? Houve uma publicação depois de criá-las? |
| A publicação falhou | Abra o registro da publicação no painel e copie o erro para o Claude Code. Peça para ele corrigir. |
| Enviou, mas o e-mail não chegou | Olhe a pasta de spam. Na primeira vez é comum cair lá: marque como "não é spam". |
| A Cloudflare recusou o envio | O domínio do remetente precisa ser o mesmo que você ativou no Email Sending. |
| O token não funciona | Veja a seção "Conferir o token sem mostrar na tela". |

## Conferir o token sem mostrar na tela

Se quiser testar o token antes de guardar, faça assim no servidor. O `read -rs` recebe o que você cola sem mostrar na tela e sem guardar no histórico de comandos:

```bash
read -rsp 'Cole o token e aperte Enter: ' T && echo && curl -s "https://api.cloudflare.com/client/v4/user/tokens/verify" -H "Authorization: Bearer $T" | grep -o '"status":"[a-z]*"'; unset T
```

A resposta certa é `"status":"active"`.

Não digite o comando com o token escrito direto na linha: ele fica no histórico do servidor e aparece em qualquer gravação de tela.

## Por que tanto cuidado com o token

Quem tem esse token envia e-mail em nome do seu domínio. Três coisas deste procedimento protegem você:

1. O token fica só na Cloudflare, como variável secreta. O código no GitHub é público e não tem nada que precise ser escondido.
2. O visitante fala com a função do seu site, e não com a API de e-mail. Ele não tem como ver nem usar o token.
3. Remetente e destinatário são fixos. O chat só serve para falar com você, e não para enviar e-mail a outras pessoas.

## Se o token aparecer em algum lugar

Tela gravada, conversa, captura de tela, commit: se o token apareceu, considere que vazou.

1. No painel da Cloudflare: foto do perfil, **My Profile**, **API Tokens**. Ache o token e clique em **Delete** ou em **Roll**.
2. Crie um token novo (passo 1).
3. Troque o valor da variável `CLOUDFLARE_API_TOKEN` no projeto do Pages (passo 2) e publique o site de novo.

Token apagado não serve para mais nada, mesmo que alguém tenha copiado. Na dúvida, apague.

## Próximo passo, se o chat começar a receber spam

Peça ao Claude Code para acrescentar o **Turnstile** da Cloudflare ao chat. É a verificação "não sou um robô" da própria Cloudflare, gratuita, e a função passa a conferir a resposta antes de enviar o e-mail.
