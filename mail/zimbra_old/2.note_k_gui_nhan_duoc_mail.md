# Note Zimbra

## Các lý do không gửi nhận được mail

- Hết tài nguyên disk, RAM, CPU
- Check bản ghi MX
- Tên miền hết hạn
- Mail bị kẹt trong queue không gửi đi được (check log, check queue. Nếu có thì xóa queue đi rồi gửi lại)
- Firewall chặn (check các port email service có được mở hay không 25, 587, 465, 110, 995 https://wiki.matbao.net/pop3-smtp-imap-la-gi-danh-sach-port-pop3-va-smtp-port-tren-email-server/)
- Chết service MTA (Zimbra hay bị)

## Log gửi và nhận mail nhận:

Gửi:
```sh
Aug 25 11:12:58 mail postfix/amavisd/smtpd[7598]: AE29D1036B5: client=localhost[127.0.0.1]
Aug 25 11:12:58 mail postfix/cleanup[6612]: AE29D1036B5: message-id=<CAJed2RO7hmbdoPY1azmynErWGtk40rrt+rG1cowgqmVQDDJD2Q@mail.gmail.com>
Aug 25 11:12:58 mail postfix/qmgr[5374]: AE29D1036B5: from=<dungz1207@gmail.com>, size=5064, nrcpt=1 (queue active)
Aug 25 11:12:58 mail postfix/smtp[6613]: 1A75B1036A8: to=<user1@dungdb.xyz>, relay=127.0.0.1[127.0.0.1]:10024, delay=8.6, delays=0.68/0/0.01/7.9, dsn=2.0.0, status=sent (250 2.0.0 from MTA(smtp:[127.0.0.1]:10025): 250 2.0.0 Ok: queued as AE29D1036B5)
Aug 25 11:12:58 mail postfix/qmgr[5374]: 1A75B1036A8: removed
Aug 25 11:12:58 mail postfix/lmtp[6616]: A8E291036B4: to=<admin@dungdb.xyz>, relay=mail.dungdb.xyz[103.124.94.119]:7025, delay=0.2, delays=0.01/0/0.09/0.09, dsn=2.1.5, status=sent (250 2.1.5 Delivery OK)
Aug 25 11:12:58 mail postfix/qmgr[5374]: A8E291036B4: removed
Aug 25 11:12:58 mail postfix/lmtp[7599]: AE29D1036B5: to=<user1@dungdb.xyz>, relay=mail.dungdb.xyz[103.124.94.119]:7025, delay=0.21, delays=0.01/0.01/0.09/0.1, dsn=2.1.5, status=sent (250 2.1.5 Delivery OK)
Aug 25 11:12:58 mail postfix/qmgr[5374]: AE29D1036B5: removed
Aug 25 11:16:11 mail postfix/anvil[7594]: statistics: max connection rate 1/60s for (smtpd:209.85.208.169) at Aug 25 11:12:48
Aug 25 11:16:11 mail postfix/anvil[7594]: statistics: max connection count 1 for (smtpd:209.85.208.169) at Aug 25 11:12:48
Aug 25 11:16:11 mail postfix/anvil[7594]: statistics: max cache size 1 at Aug 25 11:12:48
Aug 25 11:16:48 mail postfix/amavisd/smtpd[6615]: timeout after END-OF-MESSAGE from localhost[127.0.0.1]
Aug 25 11:16:48 mail postfix/amavisd/smtpd[6615]: disconnect from localhost[127.0.0.1] ehlo=1 mail=1 rcpt=1 data=1 commands=4
Aug 25 11:16:49 mail postfix/dkimmilter/smtpd[6617]: timeout after END-OF-MESSAGE from localhost[127.0.0.1]
Aug 25 11:16:49 mail postfix/dkimmilter/smtpd[6617]: disconnect from localhost[127.0.0.1] ehlo=1 mail=1 rcpt=1 data=1 commands=4
Aug 25 11:16:49 mail postfix/amavisd/smtpd[6621]: timeout after END-OF-MESSAGE from localhost[127.0.0.1]
Aug 25 11:16:49 mail postfix/amavisd/smtpd[6621]: disconnect from localhost[127.0.0.1] ehlo=1 mail=1 rcpt=1 data=1 commands=4
Aug 25 11:17:58 mail postfix/amavisd/smtpd[7598]: timeout after END-OF-MESSAGE from localhost[127.0.0.1]
Aug 25 11:17:58 mail postfix/amavisd/smtpd[7598]: disconnect from localhost[127.0.0.1] ehlo=1 mail=2 rcpt=2 data=2 commands=7
Aug 25 11:26:19 mail postfix/postscreen[18935]: CONNECT from [103.124.94.119]:57662 to [103.124.94.119]:25
Aug 25 11:26:19 mail postfix/postscreen[18935]: WHITELISTED [103.124.94.119]:57662
Aug 25 11:26:20 mail postfix/smtpd[18936]: connect from mail.dungdb.xyz[103.124.94.119]
Aug 25 11:26:20 mail postfix/smtpd[18936]: NOQUEUE: filter: RCPT from mail.dungdb.xyz[103.124.94.119]: <user1@dungdb.xyz>: Sender address triggers FILTER smtp-amavis:[127.0.0.1]:10026; from=<user1@dungdb.xyz> to=<dungz1207@gmail.com> proto=ESMTP helo=<mail.dungdb.xyz>
Aug 25 11:26:20 mail postfix/smtpd[18936]: 9A3361036A3: client=mail.dungdb.xyz[103.124.94.119]
Aug 25 11:26:20 mail postfix/cleanup[18939]: 9A3361036A3: message-id=<1961329432.16.1598329579707.JavaMail.zimbra@dungdb.xyz>
Aug 25 11:26:20 mail postfix/smtpd[18936]: disconnect from mail.dungdb.xyz[103.124.94.119] ehlo=1 mail=1 rcpt=1 data=1 quit=1 commands=5
Aug 25 11:26:20 mail postfix/qmgr[5374]: 9A3361036A3: from=<user1@dungdb.xyz>, size=4090, nrcpt=1 (queue active)
Aug 25 11:26:27 mail postfix/amavisd/smtpd[18942]: connect from localhost[127.0.0.1]
Aug 25 11:26:27 mail postfix/amavisd/smtpd[18942]: BC5811036C6: client=localhost[127.0.0.1]
Aug 25 11:26:27 mail postfix/cleanup[18939]: BC5811036C6: message-id=<VAoT3vgs4L43Ub@mail.dungdb.xyz>
Aug 25 11:26:27 mail postfix/qmgr[5374]: BC5811036C6: from=<admin@dungdb.xyz>, size=2769, nrcpt=1 (queue active)
Aug 25 11:26:27 mail postfix/dkimmilter/smtpd[18944]: connect from localhost[127.0.0.1]
Aug 25 11:26:27 mail postfix/dkimmilter/smtpd[18944]: C30551036C7: client=localhost[127.0.0.1]
Aug 25 11:26:27 mail postfix/cleanup[18939]: C30551036C7: message-id=<1961329432.16.1598329579707.JavaMail.zimbra@dungdb.xyz>
Aug 25 11:26:27 mail postfix/qmgr[5374]: C30551036C7: from=<user1@dungdb.xyz>, size=4555, nrcpt=1 (queue active)
Aug 25 11:26:27 mail postfix/smtp[18940]: 9A3361036A3: to=<dungz1207@gmail.com>, relay=127.0.0.1[127.0.0.1]:10026, delay=7.9, delays=0.61/0.01/0.02/7.2, dsn=2.0.0, status=sent (250 2.0.0 from MTA(smtp:[127.0.0.1]:10030): 250 2.0.0 Ok: queued as C30551036C7)
Aug 25 11:26:27 mail postfix/qmgr[5374]: 9A3361036A3: removed
Aug 25 11:26:28 mail postfix/lmtp[18943]: BC5811036C6: to=<admin@dungdb.xyz>, relay=mail.dungdb.xyz[103.124.94.119]:7025, delay=0.39, delays=0.01/0.01/0.2/0.17, dsn=2.1.5, status=sent (250 2.1.5 Delivery OK)
Aug 25 11:26:28 mail postfix/qmgr[5374]: BC5811036C6: removed
Aug 25 11:26:28 mail postfix/amavisd/smtpd[18949]: connect from localhost[127.0.0.1]
Aug 25 11:26:28 mail postfix/amavisd/smtpd[18949]: AFFDB1036C6: client=localhost[127.0.0.1]
Aug 25 11:26:28 mail postfix/cleanup[18939]: AFFDB1036C6: message-id=<1961329432.16.1598329579707.JavaMail.zimbra@dungdb.xyz>
Aug 25 11:26:28 mail postfix/amavisd/smtpd[18949]: disconnect from localhost[127.0.0.1] ehlo=1 mail=1 rcpt=1 data=1 quit=1 commands=5
Aug 25 11:26:28 mail postfix/qmgr[5374]: AFFDB1036C6: from=<user1@dungdb.xyz>, size=5561, nrcpt=1 (queue active)
Aug 25 11:26:28 mail postfix/smtp[18940]: C30551036C7: to=<dungz1207@gmail.com>, relay=127.0.0.1[127.0.0.1]:10032, delay=0.94, delays=0.09/0.01/0/0.84, dsn=2.0.0, status=sent (250 2.0.0 from MTA(smtp:[127.0.0.1]:10025): 250 2.0.0 Ok: queued as AFFDB1036C6)
Aug 25 11:26:28 mail postfix/qmgr[5374]: C30551036C7: removed
Aug 25 11:26:34 mail postfix/smtp[18950]: AFFDB1036C6: to=<dungz1207@gmail.com>, relay=gmail-smtp-in.l.google.com[74.125.203.26]:25, delay=6, delays=0.01/0.01/2.3/3.7, dsn=2.0.0, status=sent (250 2.0.0 OK  1598329594 co23si1094292pjb.117 - gsmtp)
Aug 25 11:26:34 mail postfix/qmgr[5374]: AFFDB1036C6: removed
```

Nhận:
```sh
Aug 25 11:31:27 mail postfix/amavisd/smtpd[18942]: timeout after END-OF-MESSAGE from localhost[127.0.0.1]
Aug 25 11:31:27 mail postfix/amavisd/smtpd[18942]: disconnect from localhost[127.0.0.1] ehlo=1 mail=1 rcpt=1 data=1 commands=4
Aug 25 11:31:27 mail postfix/dkimmilter/smtpd[18944]: timeout after END-OF-MESSAGE from localhost[127.0.0.1]
Aug 25 11:31:27 mail postfix/dkimmilter/smtpd[18944]: disconnect from localhost[127.0.0.1] ehlo=1 mail=1 rcpt=1 data=1 commands=4
Aug 25 11:32:00 mail postfix/postscreen[22148]: CONNECT from [209.85.208.171]:38585 to [103.124.94.119]:25
Aug 25 11:32:06 mail postfix/postscreen[22148]: PASS NEW [209.85.208.171]:38585
Aug 25 11:32:06 mail postfix/smtpd[22590]: connect from mail-lj1-f171.google.com[209.85.208.171]
Aug 25 11:32:07 mail postfix/smtpd[22590]: Anonymous TLS connection established from mail-lj1-f171.google.com[209.85.208.171]: TLSv1.2 with cipher ECDHE-RSA-AES128-GCM-SHA256 (128/128 bits)
Aug 25 11:32:08 mail postfix/smtpd[22590]: NOQUEUE: filter: RCPT from mail-lj1-f171.google.com[209.85.208.171]: <dungz1207@gmail.com>: Sender address triggers FILTER smtp-amavis:[127.0.0.1]:10026; from=<dungz1207@gmail.com> to=<user1@dungdb.xyz> proto=ESMTP helo=<mail-lj1-f171.google.com>
Aug 25 11:32:08 mail postfix/smtpd[22590]: NOQUEUE: filter: RCPT from mail-lj1-f171.google.com[209.85.208.171]: <dungz1207@gmail.com>: Sender address triggers FILTER smtp-amavis:[127.0.0.1]:10024; from=<dungz1207@gmail.com> to=<user1@dungdb.xyz> proto=ESMTP helo=<mail-lj1-f171.google.com>
Aug 25 11:32:08 mail postfix/smtpd[22590]: 9EDDE1036C7: client=mail-lj1-f171.google.com[209.85.208.171]
Aug 25 11:32:09 mail postfix/cleanup[22622]: 9EDDE1036C7: message-id=<CAJed2RNh18tFRvH9JUBrTDw+eCqzHY+_TBDxKNWB9ETvw95VLQ@mail.gmail.com>
Aug 25 11:32:09 mail postfix/qmgr[5374]: 9EDDE1036C7: from=<dungz1207@gmail.com>, size=6927, nrcpt=1 (queue active)
Aug 25 11:32:09 mail postfix/smtpd[22590]: disconnect from mail-lj1-f171.google.com[209.85.208.171] ehlo=2 starttls=1 mail=1 rcpt=1 data=1 quit=1 commands=7
Aug 25 11:32:17 mail postfix/amavisd/smtpd[18942]: connect from localhost[127.0.0.1]
Aug 25 11:32:17 mail postfix/amavisd/smtpd[18942]: CBDCA1036C8: client=localhost[127.0.0.1]
Aug 25 11:32:17 mail postfix/cleanup[22622]: CBDCA1036C8: message-id=<VAX60zFiZZYDc6@mail.dungdb.xyz>
Aug 25 11:32:17 mail postfix/qmgr[5374]: CBDCA1036C8: from=<admin@dungdb.xyz>, size=4465, nrcpt=1 (queue active)
Aug 25 11:32:17 mail postfix/amavisd/smtpd[18942]: CFD2E1036C9: client=localhost[127.0.0.1]
Aug 25 11:32:17 mail postfix/cleanup[22622]: CFD2E1036C9: message-id=<CAJed2RNh18tFRvH9JUBrTDw+eCqzHY+_TBDxKNWB9ETvw95VLQ@mail.gmail.com>
Aug 25 11:32:17 mail postfix/qmgr[5374]: CFD2E1036C9: from=<dungz1207@gmail.com>, size=7816, nrcpt=1 (queue active)
Aug 25 11:32:17 mail postfix/smtp[22623]: 9EDDE1036C7: to=<user1@dungdb.xyz>, relay=127.0.0.1[127.0.0.1]:10024, delay=9.2, delays=0.88/0.01/0.01/8.3, dsn=2.0.0, status=sent (250 2.0.0 from MTA(smtp:[127.0.0.1]:10025): 250 2.0.0 Ok: queued as CFD2E1036C9)
Aug 25 11:32:17 mail postfix/qmgr[5374]: 9EDDE1036C7: removed
Aug 25 11:32:18 mail postfix/lmtp[22627]: CFD2E1036C9: to=<user1@dungdb.xyz>, relay=mail.dungdb.xyz[103.124.94.119]:7025, delay=0.23, delays=0.01/0.01/0.11/0.1, dsn=2.1.5, status=sent (250 2.1.5 Delivery OK)
Aug 25 11:32:18 mail postfix/qmgr[5374]: CFD2E1036C9: removed
Aug 25 11:32:18 mail postfix/lmtp[22626]: CBDCA1036C8: to=<admin@dungdb.xyz>, relay=mail.dungdb.xyz[103.124.94.119]:7025, delay=0.26, delays=0.01/0.01/0.13/0.11, dsn=2.1.5, status=sent (250 2.1.5 Delivery OK)
Aug 25 11:32:18 mail postfix/qmgr[5374]: CBDCA1036C8: removed
```

## Nếu mail gửi đi bị gửi vào hòm thư rác thì thêm 2 bản ghi sau để tăng độ tin tưởng

    _dmarc v=DMARC1; p=none; rua=mailto:mailauth-reports@dungdb.xyz
    @ v=spf1 +a +mx +ip4:103.124.94.119 include:_spf.email-nhanhoa.net ~all

## Check queue 

https://wiki.zimbra.com/wiki/Managing-The-Postfix-Queues

https://dilliganesh.wordpress.com/2016/11/04/how-to-check-queue-status-to-hold-deferred-and-active/

Check all queue

    /opt/zimbra/libexec/zmqstat

Check current mail queue status

    su zimbra
    /opt/zimbra/common/sbin/postqueue -p

To Check current hold messages

    /opt/zimbra/common/sbin/postsuper -h ALL

Delete Single Message From Queue

    /opt/zimbra/common/sbin/postsuper -d [MSGID From postqueue -p]

Delete ALL Messages From Queue

    /opt/zimbra/common/sbin/postsuper -d ALL

To Delete ALL Messages From The Deferred Queue

    /opt/zimbra/common/sbin/postsuper -d ALL deferred

Delete ALL Messages From The Hold Queue

    /opt/zimbra/common/sbin/postsuper -d ALL hold
