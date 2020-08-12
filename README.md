# Disrupt the Mail!
This is a first draft for mail system disrupt. Support it.

# The problems:
This planet needs a new mechanism for sending and receiving emails. We need a new format for email that fully meets the needs of modern developers. There's no need to kill the e-mail principle. You just need to update it and make it beautiful again.

### 1. Really slow
if you have a service with a millions of users — sending emails is a big pain. Just imagine mail servers communicate through chat! It's ridiculous in 2020. You can't send all emails. You have to wait every email send.

### 2. Spam
The old mechanism allows you to send a spam! We have to replace it with a new one that will not allow spam at the server level (just without any of anti-spam tools).

### 3. Сomplicated
If you sent mails you know it. You have to properly make the email (mime types, utf8 etc) 
You have to properly sign it with DKIM etc. You have to properly configure all servers, etc. This is a nightmare for developers who just want to send a valid emails to users.


# The Solution
This is written for those who already know how mail, smtp, postfix, sendmail etc. works. For those who have set up servers and sent messages.

### 1. JSON everywhere
We need a modern and cool format. You need a format that's fun to play with. So there's no misunderstandings. Totally for people. 

- Markdown support (it could be the best safe email format)
- Any categories with some official: news, newsletter, invoice, confirmation (email for example), ticket, ads (why not), etc
- Files (any files, base64, links etc)
- Call to actions (pay, unsubscribe etc.)
- ID (like chats), 
- Unix/Epoch dates everywhere

Simple example:
You have json envelope with 2 fields only
- Email (all fields) *required
- Signature (hash of Email all fields) *required

```json
{
    "email":{
    "id":"newsletter.june.2020.first",
    "from":"hello@smtpe.org SMTPE Foundation",
    "to":["james.miller@nice.com James Miller", "martha@yahoo.com Martha"],
    "cc":["martha@yahoo.com Martha"],
    "bcc":["martin@apple.com"],
    "subj": "UTF8 subject by default",
    "created": 1597221012,
    "body":{
        "text":"some text body",
        "markdown": "## Some markdown body",
        "html": "<b>Some HTML Body</b>"
    },
    "files":[
        {"name":"image.jpg", "type":"image/jpg", "body":"wf1b27OWudu...Ogp+mmUf5mo"},
        {"name":"doc.pdf", "type":"document/pdf", "link":"https://apple.com/nice.pdf"}
    ],
    "type":"newsletter",
    "unsibscribe":"link.com/unsubscribe"
    },
    "signature":{
        "selector":"dkim",
        "type":"rsa-sha256",
        "hash":"wf1b27OWdus...ff24fg56kl5mo="
    }
}
```

### 2. No MX records anymore
The main reason is to give a more flexible server structure. To simplify the development of electronic mail.

1. For each domain you have to write a TXT fields in DNS. Servers to accept mail together with ports. No required 25, 2525, 487 etc ports. Use any ports you want.
```
_inbox IN TXT "214.31.1.1:7000,194.11.41.45:9999" //server to accept mail from clients
```
2. signature for every mail (a public key for domain — such as DKIM)
```
_sign IN TXT "k=rsa; p=MIGfMA0GCSq...KblfgE68m0X90riYKwe1kDhWt7wIDAQAB" //signature for every mail
```
3. List of valid IP, Hosts etc for email sending (spf)
```
_whitelist IN TXT "214.31.1.1:7000,194.11.41.45:9999, mx, a" //spf like
```

### 3. HTTPS instead any other
Now any programmer can write his just HTTP server. Since the new format is JSON, all communication between servers can pass through simple POST requests. This is simple and clear. 

Each mail server that accepts mail has only one official endpoint:
#### POST /

So if your mail server IP is 119.10.11.12 your server have to accept all emails on
https://119.10.11.12/

or if your server is newmail.nice.com your server have to accept all emails on
https://newmail.nice.com/

```
POST / HTTP/1.1
Host: newmail.nice.com
Content-Type: application/json
Content-Length: 256

{"email":"...", "signature":{...}}
```

That's it! There's no need to complicate things. Any server that wants to send an email must send a POST request to that address with the email.

### 4. Simple Email clients
To receive mail you just need to send a request to the server with the necessary data and get everything you need from it. A more flexible system than POP, IMAP, etc. Because it is possible to develop one universal format of communication between servers. Will be soon. Suggestion welcome.


# Summary
I would like to live in a world where e-mail exists but is not a pain for developers. I am sure we can implement this concept in the near future as an alternative to classic e-mail servers. Users will not even notice.

You could support the idea with a stars here or on change.org(just kidding)

### p.s.
I realize that a lot of people have enough of what they already have. But it's not a substitute for email. It's an improvement.

The old mail will work as before. But the preference will be for the new format as the main one. If the new format is not yet made on the server, the classic method will be used. This is the main idea.
