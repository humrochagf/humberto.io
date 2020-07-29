---
title: Enviando e recebendo emails com Python
description: Aprenda como começar a enviar e ler emails automaticamente com ajuda da linguagem Python
publishDate: 2018-08-10
images:
  - /img/emails/python-mail.png
slug: enviando-e-recebendo-emails-com-python
tags: ["python", "emails"]
aliases: [
  "/blog/enviando-e-recebendo-emails-com-python"
]
draft: false

---

Um dia na terça do Python no [Calango Hacker Clube](https://calango.club/) surgiu uma dúvida de como ler emails do Gmail para poder categorizar as mensagens e automatizar o processo de leitura dos emails usando Python. Esta dúvida motivou esta postagem que tem o objetivo de ajudar a todos que tiverem dúvidas de como começar a interagir com sistemas de troca de emails utilizando Python.

![python email](/img/emails/python-mail.png)

{{< tip class="info" >}}
Esta postagem tem como alvo o público iniciante mas também pode ser útil para quem já possuí experiência na linguagem mas nunca tinha trabalhado diretamente com envio e recebimento de emails. A versão do Python utilizada para publicar esta postagem foi a 3.6.x.
{{< /tip >}}

## Os Protocolos de email

Sistemas de email são muito robustos pois são construídos em cima de protocolos muito bem estabelecidos e usados por todas as plataformas de email da internet. Estes protocolos são definidos e publicados através de documentos chamados de [RFC](https://en.wikipedia.org/wiki/Request_for_Comments) que é uma sigla para Request for Comments que lembram um pouco as [PEPs](https://www.python.org/dev/peps/) do Python só que para os protocolos que definem a base do funcionamento das comunicações entre sistemas como a internet.

Para enviar emails utilizamos o protocolo [SMTP](https://pt.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) (RFCs [821](https://tools.ietf.org/html/rfc821) e [5321](https://tools.ietf.org/html/rfc5321)) e para receber emails temos os protocolos [IMAP](https://pt.wikipedia.org/wiki/Internet_Message_Access_Protocol) (RFC [3501](https://tools.ietf.org/html/rfc3501)) e [POP](https://pt.wikipedia.org/wiki/Post_Office_Protocol) (RFCs [918](https://tools.ietf.org/html/rfc918) e [1081](https://tools.ietf.org/html/rfc1081)).

## Enviando emails

Simple Mail Transfer Protocol ou **SMTP** é um protocolo da camada de aplicação usado para o envio de emails. Ele opera sobre [TCP/IP](https://pt.wikipedia.org/wiki/TCP/IP) e sua comunicação é realizada por padrão pela porta 25 ou 587 para conexões não cifradas e 465 para conexões cifradas com [TLS/SSL](https://pt.wikipedia.org/wiki/Transport_Layer_Security).

Python já vem com baterias, por isso não precisaremos instalar nenhum pacote para fazer o envio de mensagens **SMTP**, para isso utilizaremos uma biblioteca interna do python chamada `smtplib`:

{{< highlight python "linenos=table" >}}
import smtplib

mail_from = 'origin@mail.com'
mail_to = ['destiny1@mail.com', 'destiny2@mail.com']
mail_subject = 'Hello'
mail_message_body = 'Hello World!'

mail_to_string = ', '.join(mail_to)

mail_message = f'''
From: {mail_from}
To: {mail_to_string}
Subject: {mail_subject}

{mail_message_body}
'''

server = smtplib.SMTP('localhost')
server.sendmail(mail_from, mail_to, mail_subject, mail_message)
server.quit()
{{< / highlight >}}

{{< tip class="warning" >}}
Para enviar emails como **localhost** você precisa ter um servidor de emails instalado na máquina em que o código está rodando
{{< /tip >}}

O código acima é a versão mais crua de um envido de email. Nele nós construímos a mensagem "na mão" com uma string, porém o Python possuí uma biblioteca para facilitar o nosso trabalho.

Uma outra questão do código acima é que estamos enviando o email de um servidor local e isso pode vir a ser um problema pois a rede de envios de emails funciona através de uma **rede de confiança**. Se seu servidor local não segue as práticas recomendadas de envio de email ou seu **ip** não é muito conhecido nesta rede de confiança seus emails podem cair em caixa de **spam** e em alguns casos simplesmente ser recusado pelo destinatário.

Para resolver estas questões e garantir que nossa mensagem chegue corretamente vamos mudar um pouco o código e usar o gmail para facilitar a garantia da entrega da nossa mensagem:

{{< highlight python "linenos=table" >}}
import smtplib
from email.mime.text import MIMEText

# conexão com os servidores do google
smtp_ssl_host = 'smtp.gmail.com'
smtp_ssl_port = 465
# username ou email para logar no servidor
username = 'origin@gmail.com'
password = 'password'

from_addr = 'origin@gmail.com'
to_addrs = ['destiny@gmail.com']

# a biblioteca email possuí vários templates
# para diferentes formatos de mensagem
# neste caso usaremos MIMEText para enviar
# somente texto
message = MIMEText('Hello World')
message['subject'] = 'Hello'
message['from'] = from_addr
message['to'] = ', '.join(to_addrs)

# conectaremos de forma segura usando SSL
server = smtplib.SMTP_SSL(smtp_ssl_host, smtp_ssl_port)
# para interagir com um servidor externo precisaremos
# fazer login nele
server.login(username, password)
server.sendmail(from_addr, to_addrs, message.as_string())
server.quit()
{{< / highlight >}}

Caso esteja enfrentando problemas para se conectar ao google será preciso liberar o "Acesso a app menos seguro".

O google bloqueia acessos de aplicações que ele julgar como não seguindo os padrões de segurança, o problema é que não fica muito claro quais os padrões requisitados além de não ser uma tarefa muito trivial para quem está realizando seus primeiros testes de comunicação com um servidor de emails.

Entretanto, para que isso não seja um empecilho em seus primeiros passos no mundo do envio e recebimento de emails, você pode liberar o acesso de apps mesmos seguros através das configurações de segurança da sua conta em [https://myaccount.google.com/u/0/security?hl=pt](https://myaccount.google.com/u/0/security?hl=pt):

![Acesso a app menos seguro](/img/emails/menos-seguro.png)

Com isso passaremos a utilizar o google como nosso intermediário (**gateway**) para enviar as mensagens de emails. Mas tenha em mente que mesmo com o google como seu **gateway** existe uma política de envio de mensagens de email, cuidado com os **SPAMS**.

![SPAM](/img/memes/spam.jpg)

## Recebendo emails

O protocolo **IMAP** Internet Message Access Protocol é usado para receber emails e assim como o **SMTP**, opera na camada de aplicação sobre **TCP/IP**, porém utiliza as portas 143 para conexão não cifrada e 993 para conexão cifrada com **SSL**.

Além do **IMAP**, podemos também receber emails através do protocolo **POP** Post Office Protocol, mas atualmente o protocolo **IMAP** é o mais indicado para esta tarefa pois com ele é possível manter todos os seus clientes de email sincronizados e organizados, além da possibilidade de acessar mais do que somente a caixa de entradas do seu email.

O processo de recebimento é mais complexo que o envio, pois dentro dele está incluso a busca pela mensagem que você deseja ler e sua decodificação. No código a seguir tentarei comentar todas estas etapas:

{{< highlight python "linenos=table" >}}
import email
import imaplib

EMAIL = 'mymail@mail.com'
PASSWORD = 'password'
SERVER = 'imap.gmail.com'


# abriremos uma conexão com SSL com o servidor de emails
# logando e navegando para a inbox
mail = imaplib.IMAP4_SSL(SERVER)
mail.login(EMAIL, PASSWORD)
# selecionamos a caixa de entrada neste caso
# mas qualquer outra caixa pode ser selecionada
mail.select('inbox')

# faremos uma busca com o critério ALL para pegar
# todos os emails da inbox, esta busca retorna
# o status da operação e uma lista com
# os ids dos emails
status, data = mail.search(None, 'ALL')
# data é uma lista com ids em blocos de bytes separados
# por espaço neste formato: [b'1 2 3', b'4 5 6']
# então para separar os ids primeiramente criaremos
# uma lista vazia
mail_ids = []
# e em seguida iteramos pelo data separando os blocos
# de bytes e concatenando a lista resultante com nossa
# lista inicial
for block in data:
    # a função split chamada sem nenhum parâmetro
    # transforma texto ou bytes em listas usando como
    # ponto de divisão o espaço em branco:
    # b'1 2 3'.split() => [b'1', b'2', b'3']
    mail_ids += block.split()

# agora para cada id baixaremos o email
# e extrairemos seu conteúdo
for i in mail_ids:
    # a função fetch baixa o email passando id e o formato
    # em que você deseja que a mensagem venha
    status, data = mail.fetch(i, '(RFC822)')

    # data no formato '(RFC822)' vem em uma lista com a
    # tupla onde o conteúdo está e o byte de fechamento b')'
    # por isso vamos iterar pelo data extraindo a tupla
    for response_part in data:
        # se for a tupla a extraímos o conteúdo
        if isinstance(response_part, tuple):
            # o primeiro elemento da tupla é o cabeçalho
            # de formatação e o segundo elemento possuí o
            # conteúdo que queremos extrair
            message = email.message_from_bytes(response_part[1])

            # com o resultado conseguimos pegar as
            # informações de quem enviou o email e o assunto
            mail_from = message['from']
            mail_subject = message['subject']

            # agora para o texto do email precisamos de um
            # pouco mais de trabalho pois ele pode vir em texto puro
            # ou em multipart, se for texto puro é só ir para o
            # else e extraí-lo do payload, caso contrário temos que
            # separar o que é anexo e extrair somente o texto
            if message.is_multipart():
                mail_content = ''

                # no caso do multipart vem junto com o email
                # anexos e outras versões do mesmo email em
                # diferentes formatos como texto imagem e html
                # para isso vamos andar pelo payload do email
                for part in message.get_payload():
                    # se o conteúdo for texto text/plain que é o
                    # texto puro nós extraímos
                    if part.get_content_type() == 'text/plain':
                        mail_content += part.get_payload()
            else:
                mail_content = message.get_payload()

            # por fim vamos mostrar na tela o resultado da extração
            print(f'From: {mail_from}')
            print(f'Subject: {mail_subject}')
            print(f'Content: {mail_content}')
{{< / highlight >}}

Neste código extraímos somente o conteúdo em texto puro do email, porém há muito mais coisas que podem ser tratadas como conteúdo em **html** e extração de anexos mas estes casos ficaram para uma próxima postagem.

## BONUS: Limpando Caixas de Email Lotadas

Recentemente minha caixa de emails lotou e eu precisei dar uma faxina para liberar espaço, mas o volume era tão grande que a interface do gmail não estava dando conta do recado.

Para isso é possível utilizar o Python para nos ajudar com esta questão:

{{< highlight python "linenos=table" >}}
import imaplib

# caso sua caixa de emails esteja muito cheia
# use esta variável para aumentar o limite do
# tamanho da resposta
imaplib._MAXLINE = 1000000

EMAIL = 'mymail@gmail.com'
PASSWORD = 'password'
SERVER = 'imap.gmail.com'

mail = imaplib.IMAP4_SSL(SERVER)
mail.login(EMAIL, PASSWORD)
# selecionando a caixa que desejamos limpar
mail.select('caixa_lotada')

status, search_data = mail.search(None, 'ALL')

mail_ids = []

for block in search_data:
    mail_ids += block.split()

# definindo o range da operação
start = mail_ids[0].decode()
end = mail_ids[-1].decode()

# movendo os emails para a lixeira
# este passo é específico do gmail
# que não permite a exclusão direta
mail.store(f'{start}:{end}'.encode(), '+X-GM-LABELS', '\\Trash')

# acessando a lixeira do gmail
mail.select('[Gmail]/Trash')
# marcando todos os emails da lixeira para remoção
mail.store("1:*", '+FLAGS', '\\Deleted')

# removendo permanentemente os emails
mail.expunge()

# fechando as caixas de email
mail.close()
# encerrando a conexão
mail.logout()
{{< / highlight >}}

Caso o volume de emails seja muito grande é possível também fazer um loop agrupando a remoção em blocos de remoção menores.

Para buscas mais elaboradas com o comando `search` você pode acessar a [RFC 3501](https://tools.ietf.org/html/rfc3501) que define o protocolo **IMAP** e contém a descrição detalhada de todos os parâmetros de busca possíveis bem como mais detalhes para todos os outros comandos de manipulação de emails.

{{< tip class="danger" >}}
**ATENÇÃO**: Teste bastante as buscas antes de realizar a remoção efetivamente pois esta remoção não tem volta.
{{< /tip >}}
