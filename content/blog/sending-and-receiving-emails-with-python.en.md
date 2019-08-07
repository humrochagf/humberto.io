---
title: Sending and receiving emails with Python
description: Learn how to send, receive, and automate your email tasks using Python
publishDate: 2018-08-10
images:
  - /img/emails/python-mail.png
tags: ["python", "emails"]
draft: false

---

Someday, during the Python Tuesdays event at [Calango Hacker Club](https://calango.club/), one of the participants came with a question on how to automate its email reading process at Gmail categorizing their messages into something that could be important and something that is not. That question motivated me looking after how to that, and then into this post that maybe can help someone that is trying to do something similar with Python.

![python email](/img/emails/python-mail.png)

{{% tip class="info" %}}
This post aims to someone that is starting with Python but also can be useful to knowledgeable users who haven't worked with email before. The version of Python used to make this post was 3.6.x.
{{% /tip %}}

## The email Protocols

Email systems are robust because they rely upon well-established protocols used by all email platforms across the internet. These protocols are defined and published by documents called [RFC](https://en.wikipedia.org/wiki/Request_for_Comments) that stands for Request for Comments and resemble the [PEPs](https://www.python.org/dev/peps/) from Python, but for protocols and patterns that define the operation of communication systems through the internet.

To send emails, we use the [SMTP](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) protocol  (RFCs [821](https://tools.ietf.org/html/rfc821) e [5321](https://tools.ietf.org/html/rfc5321)), and to receive them, we use [IMAP](https://en.wikipedia.org/wiki/Internet_Message_Access_Protocol) (RFC [3501](https://tools.ietf.org/html/rfc3501)) or [POP](https://en.wikipedia.org/wiki/Post_Office_Protocol) (RFCs [918](https://tools.ietf.org/html/rfc918) e [1081](https://tools.ietf.org/html/rfc1081)) protocols.

## Sending emails

Simple Mail Transfer Protocol or **SMTP** is a protocol from the application layer used to send emails. It operates over [TCP/IP](https://en.wikipedia.org/wiki/Internet_protocol_suite), and your communication is often made using the port 25 or 587 for unencrypted connections and 465 for [TLS/SSL](https://en.wikipedia.org/wiki/Transport_Layer_Security)
 encrypted connections.

Python comes with batteries included, so we don't need to install any external package to deal with **SMTP**. We use a library called `smtplib`:

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

{{% tip class="warning" %}}
To send emails as **localhost**, you need to have an email server installed at the machine where the code is running.
{{% /tip %}}

The code above is a raw version of an email sent. We built the message from the text so you can see the basic structure of an email message, but Python has a library to create more complex emails.

We are sending the email from a **local server**, and it can be an issue because the email network works as a **chain of trust**. If your local server doesn't follow the best practices of email sending, or if the chain doesn't know your **IP**, your messages can be marked as **SPAM**, or be dropped by the receiver.

To solve these questions, and to grant its delivery, let's change the code a little to use Gmail as our gateway:

{{< highlight python "linenos=table" >}}
import smtplib
from email.mime.text import MIMEText

# connect with Google's servers
smtp_ssl_host = 'smtp.gmail.com'
smtp_ssl_port = 465
# use username or email to log in
username = 'origin@gmail.com'
password = 'password'

from_addr = 'origin@gmail.com'
to_addrs = ['destiny@gmail.com']

# the email lib has a lot of templates
# for different message formats,
# on our case we will use MIMEText
# to send only text
message = MIMEText('Hello World')
message['subject'] = 'Hello'
message['from'] = from_addr
message['to'] = ', '.join(to_addrs)

# we'll connect using SSL
server = smtplib.SMTP_SSL(smtp_ssl_host, smtp_ssl_port)
# to interact with the server, first we log in
# and then we send the message
server.login(username, password)
server.sendmail(from_addr, to_addrs, message.as_string())
server.quit()
{{< / highlight >}}

In case you have problems to connect at Google, you need to enable the "Less secure app access".

Google blocks access from apps that it judges as not following its security standards, the problem is they don't have a clear explanation of what these standards are, besides that it's not a trivial task for whom is starting and doing the first test to struggle with that.

That said if you are facing this issue, you can enable access to make your tests by accessing  [https://myaccount.google.com/u/0/security?hl=en](https://myaccount.google.com/u/0/security?hl=en):

![Less secure access](/img/emails/less-secure.png)

Now we can use Google as our **gateway** to send the messages, but keep in mind that even with Google as your **gateway** there is a policy of email sending, so be careful with **SPAMS**.

![SPAM](/img/memes/spam.jpg)

## Receiving emails

The **IMAP** Internet Message Access Protocol is used to receive emails, and as on **SMTP** it operates at the application layer over **TCP/IP**. The port used for its connections is 143 for unencrypted and 993 for encrypted.

Another protocol that works for this task is **POP** Post Office Protocol, but **IMAP** is better due to its synchronization between the client and the server and also the ability to access more than the email inbox.

The process of receiving emails is more complicated than sending because you also have to search for the message and decode it:

{{< highlight python "linenos=table" >}}
import email
import imaplib

EMAIL = 'mymail@mail.com'
PASSWORD = 'password'
SERVER = 'imap.gmail.com'

# connect to the server and go to its inbox
mail = imaplib.IMAP4_SSL(SERVER)
mail.login(EMAIL, PASSWORD)
# we choose the inbox but you can select others
mail.select('inbox')

# we'll search using the ALL criteria to retrieve
# every message inside the inbox
# it will return with its status and a list of ids
status, data = mail.search(None, 'ALL')
# the list returned is a list of bytes separated
# by white spaces on this format: [b'1 2 3', b'4 5 6']
# so, to separate it first we create an empty list
mail_ids = []
# then we go through the list splitting its blocks
# of bytes and appending to the mail_ids list
for block in data:
    # the split function called without parameter
    # transforms the text or bytes into a list using
    # as separator the white spaces:
    # b'1 2 3'.split() => [b'1', b'2', b'3']
    mail_ids += block.split()

# now for every id we'll fetch the email
# to extract its content
for i in mail_ids:
    # the fetch function fetch the email given its id
    # and format that you want the message to be
    status, data = mail.fetch(i, '(RFC822)')

    # the content data at the '(RFC822)' format comes on
    # a list with a tuple with header, content, and the closing
    # byte b')'
    for response_part in data:
        # so if its a tuple...
        if isinstance(response_part, tuple):
            # we go for the content at its second element
            # skipping the header at the first and the closing
            # at the third
            message = email.message_from_bytes(response_part[1])

            # with the content we can extract the info about
            # who sent the message and its subject
            mail_from = message['from']
            mail_subject = message['subject']

            # then for the text we have a little more work to do
            # because it can be in plain text or multipart
            # if its not plain text we need to separate the message
            # from its annexes to get the text
            if message.is_multipart():
                mail_content = ''

                # on multipart we have the text message and
                # another things like annex, and html version
                # of the message, in that case we loop through
                # the email payload
                for part in message.get_payload():
                    # if the content type is text/plain
                    # we extract it
                    if part.get_content_type() == 'text/plain':
                        mail_content += part.get_payload()
            else:
                # if the message isn't multipart, just extract it
                mail_content = message.get_payload()

            # and then let's show its result
            print(f'From: {mail_from}')
            print(f'Subject: {mail_subject}')
            print(f'Content: {mail_content}')
{{< / highlight >}}

On this code, we extracted only the plain text of the email, but there's much more you can extract, like the **HTML** content and the annexed files, but this can be a topic for a next post.

## BONUS: Automate the cleaning of bloated mailboxes

Recently, my mailbox got bloated, and I had to do a clean up to get some space, but the high amount of messages overloaded the Gmail interface.

So, to solve that, and also for automating this tedious manual task, I wrote this:

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
# select the box you want to clean
mail.select('bloated_box')

status, search_data = mail.search(None, 'ALL')

mail_ids = []

for block in search_data:
    mail_ids += block.split()

# define the range for the operation
start = mail_ids[0].decode()
end = mail_ids[-1].decode()

# move the emails to the trash
# this step is Gmail specific because
# it doesn't allow excluding messages
# outside the trash
mail.store(f'{start}:{end}'.encode(), '+X-GM-LABELS', '\\Trash')

# access the Gmail trash
mail.select('[Gmail]/Trash')
# mark the emails to be deleted
mail.store("1:*", '+FLAGS', '\\Deleted')

# remove permanently the emails
mail.expunge()

# close the mailboxes
mail.close()
# close the connection
mail.logout()
{{< / highlight >}}

In case the number of messages is too big, you can make a loop to group it into smaller chunks to remove.

For more elaborated searches using the search `search` command, you can look at the [RFC 3501](https://tools.ietf.org/html/rfc3501) that defines the **IMAP** protocol and describes all possible search parameters, and other commands with details.

{{% tip class="danger" %}}
**WARNING:** Double-check your searches before doing any destructive operation like removal because you can't go back.
{{% /tip %}}
