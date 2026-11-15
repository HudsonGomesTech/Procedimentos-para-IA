# Claude Code editando o seu site no GitHub, sem vazar o token

Procedimento do vídeo "Claude Code publicando por você", do canal [Hudson Gomes Tech](https://www.youtube.com/@HudsonGomesTech).

A ideia: você pede a mudança em português, o Claude Code altera os arquivos do site e envia ao GitHub. Se o site está ligado ao Cloudflare Pages, ele se atualiza sozinho em seguida.

Este passo a passo também corrige o que deu errado na gravação: o token do GitHub apareceu na tela. Mais abaixo está o motivo e como evitar.

## O que você precisa

- Um repositório no GitHub com o seu site.
- Um computador ou servidor Linux com acesso à internet.
- Uma conta do Claude com acesso ao Claude Code.

## 1. Criar o token no GitHub

O token é a chave que deixa o Claude Code gravar no seu repositório. Crie o mais limitado possível.

1. No GitHub: foto do perfil, **Settings**, **Developer settings**, **Personal access tokens**, **Fine-grained tokens**, **Generate new token**.
2. **Token name:** um nome que diga para que serve, por exemplo `claude-code-site`.
3. **Expiration:** escolha um prazo curto. Quando vencer, você cria outro.
4. **Repository access:** marque **Only select repositories** e escolha só o repositório do site.
5. **Permissions**, **Repository permissions**, **Contents:** escolha **Read and write**. Não marque mais nada.
6. Clique em **Generate token** e copie o token. Ele aparece uma única vez.

Não cole o token em conversa, e-mail, anotação ou captura de tela. Ele vai direto para o arquivo do passo 2.

## 2. Guardar o token no servidor, sem mostrar na tela

Rode os três comandos abaixo no servidor. Se você não é o usuário `root`, coloque `sudo` na frente de `mkdir`, `chmod` e `tee`.

```bash
mkdir -p /etc/credentials && chmod 700 /etc/credentials

read -rsp 'Cole o token e aperte Enter: ' T && printf 'GITHUB_TOKEN=%s\n' "$T" | tee /etc/credentials/git-token.env > /dev/null; unset T

chmod 600 /etc/credentials/git-token.env
```

O que cada parte faz:

- `read -rs` recebe o que você cola sem mostrar na tela e sem guardar no histórico de comandos.
- O arquivo fica no formato `GITHUB_TOKEN=valor`. O nome na frente é importante, veja a seção "O que deu errado na gravação".
- `chmod 700` e `chmod 600` deixam a pasta e o arquivo legíveis só para o dono.

Para conferir que gravou, sem mostrar o valor:

```bash
sed 's/=.*/=(guardado)/' /etc/credentials/git-token.env
```

A resposta certa é `GITHUB_TOKEN=(guardado)`.

## 3. Instalar o Claude Code e entrar

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Se o instalador avisar que a pasta dele não está no `PATH`, rode o comando que ele mesmo mostra na tela.

Depois crie uma pasta de trabalho, entre nela e abra o Claude Code:

```bash
mkdir -p ~/site && cd ~/site
claude
```

Na primeira vez ele pede o login: abra o endereço que aparece, entre na sua conta e cole o código de volta no terminal. Esse endereço e esse código também são só seus, não mostre para ninguém.

Abra o Claude Code na pasta de trabalho, e não dentro de `/etc/credentials`.

## 4. O pedido

Copie, troque o que está entre `< >` e cole no Claude Code:

```text
Você será o meu editor de site.

O site está no GitHub, no repositório <https://github.com/SEU-USUARIO/SEU-REPOSITORIO.git>.
O token com permissão de escrita nesse repositório está no arquivo /etc/credentials/git-token.env,
na variável GITHUB_TOKEN.

Regras sobre o token:
- Nunca mostre o valor dele na tela: nada de cat, echo, grep ou qualquer comando cuja saída contenha o token.
- Carregue o arquivo para dentro de uma variável e use só a variável.
- Não grave o token em nenhum outro arquivo, nem no endereço do repositório, nem em mensagem de commit.
- Se por algum motivo o token aparecer na tela, pare e me avise.

O que eu quero:
<descreva a mudança. Exemplo: troque a cor dos títulos de vermelho para um degradê de branco
para verde passando por amarelo, e coloque algumas animações discretas.>

Como trabalhar:
- Baixe o repositório para esta pasta, leia os arquivos e só então altere.
- Antes de enviar, me diga em poucas linhas o que mudou e em quais arquivos.
- Envie tudo em um único commit, com uma mensagem que explique a mudança.
- No fim, me diga o endereço do commit.
```

Para os pedidos seguintes basta escrever a mudança, na mesma conversa. Por exemplo: "ficou ótimo, agora troque o fundo também".

## 5. Conferir

- No GitHub, abra o repositório e veja o commit novo.
- Se o site está no Cloudflare Pages ligado a esse repositório, a publicação começa sozinha e a mudança aparece em um ou dois minutos.
- Não gostou? Peça ao Claude Code para desfazer a última mudança. O histórico do GitHub guarda todas as versões.

## O que deu errado na gravação

No vídeo, o arquivo foi criado só com o token dentro, sem o nome `GITHUB_TOKEN=` na frente. O Claude Code tentou ler o arquivo escondendo o valor, mas o comando que ele usou esperava o formato `NOME=valor`. Como não havia nome, o token inteiro saiu na tela. O próprio Claude Code percebeu, avisou e recomendou trocar o token.

O token daquela gravação foi revogado antes de o vídeo ir ao ar.

Três coisas evitam o problema, e todas estão neste procedimento:

1. O arquivo no formato `GITHUB_TOKEN=valor`, criado com `read -rs`.
2. O pedido dizendo com todas as letras que o token nunca pode aparecer na tela.
3. O token limitado a um repositório, só com a permissão **Contents**, e com prazo curto. Se vazar, o estrago é pequeno.

## Se o token aparecer mesmo assim

1. No GitHub: **Settings**, **Developer settings**, **Fine-grained tokens**, abra o token e clique em **Delete** ou **Revoke**.
2. Crie um token novo (passo 1).
3. Grave o novo no arquivo (passo 2).

Token revogado não serve para mais nada, mesmo que alguém tenha copiado. Na dúvida, revogue.
