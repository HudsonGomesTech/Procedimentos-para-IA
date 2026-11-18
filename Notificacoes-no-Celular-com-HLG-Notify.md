# Notificações no celular com o HLG Notify, sem a IA ver os tokens

Procedimento do canal [Hudson Gomes Tech](https://www.youtube.com/@HudsonGomesTech).

A ideia: o seu servidor, script, cron ou aplicativo manda avisos push direto para o seu celular usando o
[HLG Notify](https://notify.hlgcloud.com.br/docs.html). Você pede em português, e a IA (por exemplo o Claude Code)
lê a documentação, valida as credenciais, faz o primeiro envio e cria um comando `hlg-notify` para você usar em
qualquer processo.

O cuidado principal deste passo a passo é com os tokens do canal: a IA só fica sabendo **onde** eles estão, nunca o valor.

## O que você precisa

- Um computador ou servidor Linux com acesso à internet, com `curl` e `python3` (ou `jq`).
- Um celular Android com o app **HLG Notify** (Google Play).
- Um assistente de IA que trabalhe no terminal, como o Claude Code.

## 1. Criar o canal

1. Abra https://notify.hlgcloud.com.br e clique em **Criar meu canal**.
2. Você recebe o **código do canal** (algo como `hlg-xxxxxxxx`), o **publish token**, o **enroll token** e um QR Code.
3. Baixe o arquivo de **credenciais admin**. O publish token aparece uma única vez; se perder, só criando outro canal.

Os dois tokens são diferentes:

- **Publish token**: envia mensagens, lista os aparelhos e apaga o histórico. Trate como senha.
- **Enroll token**: só inscreve um aparelho no canal. É ele que vai dentro do QR, pode circular.

## 2. Inscrever o celular

No app HLG Notify, toque em **Ler QR do canal** e escaneie o QR. Sem celular inscrito, as mensagens chegam ao
servidor mas não tocam em lugar nenhum (a API responde `"devices": 0`).

## 3. Guardar os tokens no servidor, sem mostrar na tela

Rode no servidor. Se você não é o usuário `root`, coloque `sudo` na frente de `mkdir`, `chmod` e `tee`.

```bash
mkdir -p /etc/credentials && chmod 700 /etc/credentials

read -rp  'Código do canal (hlg-...): ' C
read -rsp 'Cole o PUBLISH_TOKEN e aperte Enter: ' P; echo
read -rsp 'Cole o ENROLL_TOKEN e aperte Enter: ' E; echo
printf 'CANAL=%s\nPUBLISH_TOKEN=%s\nENROLL_TOKEN=%s\n' "$C" "$P" "$E" | tee /etc/credentials/hlg-notify.env > /dev/null
unset C P E

chmod 600 /etc/credentials/hlg-notify.env
```

- `read -rs` recebe o que você cola sem mostrar na tela e sem guardar no histórico de comandos.
- O formato `NOME=valor` permite que os comandos escondam o valor quando alguém confere o arquivo.
- `chmod 600` deixa o arquivo legível só para o dono.

Para conferir sem mostrar os valores:

```bash
sed 's/=.*/=(guardado)/' /etc/credentials/hlg-notify.env
```

A resposta certa são três linhas: `CANAL=(guardado)`, `PUBLISH_TOKEN=(guardado)` e `ENROLL_TOKEN=(guardado)`.

Nunca cole os tokens na conversa com a IA.

## 4. O pedido

Abra a IA numa pasta de trabalho (não dentro de `/etc/credentials`), copie, troque o que está entre `< >` se precisar e cole:

```text
Quero usar o HLG Notify como ferramenta de notificação push para os meus aplicativos.
Leia a documentação oficial antes de começar: https://notify.hlgcloud.com.br/docs.html

As credenciais estão no arquivo </etc/credentials/hlg-notify.env>, com as variáveis
CANAL=, PUBLISH_TOKEN= e ENROLL_TOKEN=.

Regras sobre os tokens:
- Nunca mostre o valor de PUBLISH_TOKEN ou ENROLL_TOKEN na tela: nada de cat, echo, grep ou qualquer
  comando cuja saída contenha os tokens. Para conferir o arquivo, mostre só os nomes das variáveis
  com o valor mascarado.
- Carregue o arquivo com: set -a; . <arquivo>; set +a — e use só as variáveis ($PUBLISH_TOKEN, $CANAL).
- Não grave os tokens em nenhum outro arquivo e não envie para nenhum endereço além de notify.hlgcloud.com.br.
- O token não pode aparecer na lista de processos (ps): passe o cabeçalho Authorization pelo stdin do curl (curl -K -).
- Se por algum motivo um token aparecer na tela, pare e me avise.

O que eu quero, nesta ordem:
1. Confira se o arquivo existe, se as três variáveis estão preenchidas e qual a permissão dele.
2. Valide o canal e o token com GET /v1/channels/$CANAL/info e GET /v1/channels/$CANAL/devices.
   Mostre o código HTTP, o nome do canal e quantos aparelhos estão inscritos.
3. Envie uma notificação de teste (POST /v1/publish/$CANAL com title, body e priority) e mostre o
   message_id e o número de devices. Se vier devices=0, me explique como inscrever o celular.
4. Crie um comando reutilizável em /usr/local/bin/hlg-notify, para eu usar em cron, scripts e outros processos:
   - mensagem por argumento ou pela entrada padrão (ex.: comando | hlg-notify -t "Título");
   - opções -t título, -p prioridade (aceitar só de 1 a 5), -u link ao tocar (click_url),
     -g etiqueta (pode repetir), -e outro arquivo de credenciais (também pela variável HLG_NOTIFY_ENV),
     -q silencioso e -h ajuda;
   - JSON montado com escape correto (aspas, acentos, quebras de linha) usando jq ou python3, o que tiver instalado;
   - tempo limite e nova tentativa no curl, e aviso se a mensagem passar de 3500 bytes;
   - erros claros para 401 (token inválido), 404 (canal não existe) e 429 (limite de 60 envios por minuto);
   - códigos de saída: 0 enviado, 1 erro de uso ou configuração, 2 falha de rede, 3 recusado pela API,
     4 enviado mas sem aparelho inscrito.
   Teste o comando: envio normal, pela entrada padrão, prioridade inválida e token errado
   (use um token falso escrito no comando, nunca o verdadeiro).
5. Se o arquivo de credenciais estiver legível por outros usuários, aplique chmod 600 e teste o envio de novo.
6. No fim, me mostre exemplos de uso do comando e avise sobre o que pode dar problema
   (ex.: processos que não rodam como root não leem um arquivo com 600; o código de saída 4
   pode interromper scripts que usam set -e).
```

## 5. Conferir

- O celular tem que tocar com a notificação de teste em poucos segundos, mesmo com o app fechado.
- A resposta do envio tem que vir com `"devices": 1` (ou o número de celulares inscritos).
- Depois disso, em qualquer script ou cron:

```bash
hlg-notify -t "Backup" -p 2 "Concluído em 4min"
/opt/backup.sh || hlg-notify -t "BACKUP FALHOU" -p 5 "Servidor $(hostname)"
tail -n 20 /var/log/app.log | hlg-notify -t "Log do app"
```

Prioridades: **1** silencioso, **2** som sem vibrar, **3** padrão, **4** aparece na tela, **5** toca até no Não Perturbe.
Use a 5 com parcimônia.

## Se a notificação não chegar

- A resposta veio com `"devices": 0`? Nenhum celular está inscrito: escaneie o QR (passo 2).
- No app, em **Sobre**, confira se as notificações estão "Permitidas" e se o código do canal é o mesmo do arquivo.
- Veja em **Configurações** do app se o canal não está silenciado.
- Economia de bateria agressiva pode segurar as notificações: libere o app para rodar em segundo plano.

## Se o publish token vazar

O servidor do HLG Notify guarda só o hash do token, então não existe como trocar ou recuperar.

1. Crie um canal novo (passo 1) e escaneie o QR novo no celular.
2. Grave as credenciais novas no arquivo (passo 3).
3. No app, saia do canal antigo em **Configurações**, **Sair deste canal**.

Documentação completa: https://notify.hlgcloud.com.br/docs.html
