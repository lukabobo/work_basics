# Log mail

## pflogsumm

Cài đặt:

    yum -y install postfix-perl-scripts 

Thử gửi một thư và xem log hôm nay

![Imgur](https://i.imgur.com/ACD1lX4.png)

    perl /usr/sbin/pflogsumm -d yesterday /var/log/maillog

Kết quả:

![Imgur](https://i.imgur.com/zV7fx0c.png)

```
[root@mail ~]# perl /usr/sbin/pflogsumm -d yesterday /var/log/maillog
Postfix log summaries for Oct  3

Grand Totals
------------
messages

      0   received
      0   delivered
      0   forwarded
      0   deferred
      0   bounced
      0   rejected (0%)
      0   reject warnings
      0   held
      0   discarded (0%)

      0   bytes received
      0   bytes delivered
      0   senders
      0   sending hosts/domains
      0   recipients
      0   recipient hosts/domains


Per-Hour Traffic Summary
------------------------
    time          received  delivered   deferred    bounced     rejected
    --------------------------------------------------------------------
    0000-0100           0          0          0          0          0
    0100-0200           0          0          0          0          0
    0200-0300           0          0          0          0          0
    0300-0400           0          0          0          0          0
    0400-0500           0          0          0          0          0
    0500-0600           0          0          0          0          0
    0600-0700           0          0          0          0          0
    0700-0800           0          0          0          0          0
    0800-0900           0          0          0          0          0
    0900-1000           0          0          0          0          0
    1000-1100           0          0          0          0          0
    1100-1200           0          0          0          0          0
    1200-1300           0          0          0          0          0
    1300-1400           0          0          0          0          0
    1400-1500           0          0          0          0          0
    1500-1600           0          0          0          0          0
    1600-1700           0          0          0          0          0
    1700-1800           0          0          0          0          0
    1800-1900           0          0          0          0          0
    1900-2000           0          0          0          0          0
    2000-2100           0          0          0          0          0
    2100-2200           0          0          0          0          0
    2200-2300           0          0          0          0          0
    2300-2400           0          0          0          0          0

Host/Domain Summary: Message Delivery
--------------------------------------
 sent cnt  bytes   defers   avg dly max dly host/domain
 -------- -------  -------  ------- ------- -----------

Host/Domain Summary: Messages Received
---------------------------------------
 msg cnt   bytes   host/domain
 -------- -------  -----------

Senders by message count: none

Recipients by message count: none

Senders by message size: none

Recipients by message size: none

message deferral detail: none

message bounce detail (by relay): none

message reject detail: none

message reject warning detail: none

message hold detail: none

message discard detail: none

smtp delivery failures: none

Warnings: none

Fatal Errors: none

Panics: none

Master daemon messages: none
[root@mail ~]# perl /usr/sbin/pflogsumm -d today /var/log/maillog
Postfix log summaries for Oct  4

Grand Totals
------------
messages

      1   received
      1   delivered
      0   forwarded
      0   deferred
      0   bounced
      1   rejected (50%)
      0   reject warnings
      0   held
      0   discarded (0%)

    706   bytes received
    706   bytes delivered
      1   senders
      1   sending hosts/domains
      1   recipients
      1   recipient hosts/domains


Per-Hour Traffic Summary
------------------------
    time          received  delivered   deferred    bounced     rejected
    --------------------------------------------------------------------
    0000-0100           0          0          0          0          0
    0100-0200           0          0          0          0          0
    0200-0300           0          0          0          0          0
    0300-0400           0          0          0          0          0
    0400-0500           0          0          0          0          0
    0500-0600           0          0          0          0          0
    0600-0700           0          0          0          0          1
    0700-0800           0          0          0          0          0
    0800-0900           0          0          0          0          0
    0900-1000           0          0          0          0          0
    1000-1100           0          0          0          0          0
    1100-1200           0          0          0          0          0
    1200-1300           0          0          0          0          0
    1300-1400           0          0          0          0          0
    1400-1500           0          0          0          0          0
    1500-1600           0          0          0          0          0
    1600-1700           0          0          0          0          0
    1700-1800           0          0          0          0          0
    1800-1900           0          0          0          0          0
    1900-2000           1          1          0          0          0
    2000-2100           0          0          0          0          0
    2100-2200           0          0          0          0          0
    2200-2300           0          0          0          0          0
    2300-2400           0          0          0          0          0

Host/Domain Summary: Message Delivery
--------------------------------------
 sent cnt  bytes   defers   avg dly max dly host/domain
 -------- -------  -------  ------- ------- -----------
      1      706        0     4.1 s    4.1 s  gmail.com

Host/Domain Summary: Messages Received
---------------------------------------
 msg cnt   bytes   host/domain
 -------- -------  -----------
      1      706   doanbadung.xyz

Senders by message count
------------------------
      1   cent@doanbadung.xyz

Recipients by message count
---------------------------
      1   dungz1207@gmail.com

Senders by message size
-----------------------
    706   cent@doanbadung.xyz

Recipients by message size
--------------------------
    706   dungz1207@gmail.com

message deferral detail: none

message bounce detail (by relay): none

message reject detail
---------------------
  RCPT
    Relay access denied (total: 1)
           1   192.227.128.208

message reject warning detail: none

message hold detail: none

message discard detail: none

smtp delivery failures: none

Warnings
--------
  smtpd (total: 47)
        41   unknown[207.154.243.255]: SASL LOGIN authentication failed: UGF...
         3   unknown[94.102.56.238]: SASL LOGIN authentication failed: UGFzc...
         1   unknown[94.102.56.238]: SASL LOGIN authentication failed: Conne...
         1   hostname 192-227-128-208-host.colocrossing.com does not resolve...
         1   hostname zg-0915b-234.stretchoid.com does not resolve to addres...

Fatal Errors: none

Panics: none

Master daemon messages: none

```

## AWstats

https://www.server-world.info/en/note?os=CentOS_7&p=mail&f=8

http://domainhostseotool.com/you-dont-have-permission-to-access-awstatsawstats-pl-on-this-server.html

https://code.trev.id.au/you-dont-have-permission-to-access-awstats-awstats-pl-on-this-server/

Cài đặt:

    yum install epel-release
    yum install httpd
    systemctl enable httpd
    systemctl start httpd
    yum install awstats

    vi /etc/awstats/awstats.mail.doanbadung.xyz

Dòng 50: sửa

    LogFile="/usr/share/awstats/tools/maillogconvert.pl standard < /var/log/maillog |
    " 

Dòng 62: sửa

    LogType=M

line 122: comment out and add like follows

    LogFormat=1
    LogFormat="%time2 %email %email_r %host %host_r %method %url %code %bytesd"

line 848: change like follows

    LevelForBrowsersDetection=0

    LevelForOSDetection=0

    LevelForRefererAnalyze=0

    LevelForRobotsDetection=0

    LevelForSearchEnginesDetection=0

    LevelForKeywordsDetection=0

    LevelForFileTypesDetection=0

    LevelForWormsDetection=0

line 966: change like follows

    ShowMonthStats=UHB

    ShowDaysOfMonthStats=HB

    ShowDaysOfWeekStats=HB

    ShowHoursStats=HB

    ShowDomainsStats=0

    ShowHostsStats=HBL

    ShowRobotsStats=0

    ShowEMailSenders=HBML

    ShowEMailReceivers=HBML

    ShowSessionsStats=0

    ShowPagesStats=0

    ShowFileTypesStats=0

    ShowOSStats=0

    ShowBrowsersStats=0

    ShowOriginStats=0

    ShowKeyphrasesStats=0

    ShowKeywordsStats=0

    ShowMiscStats=0

    ShowHTTPErrorsStats=0

    ShowSMTPErrorsStats=1

Sửa file `/etc/httpd/conf.d/awstats.conf`

    vi /etc/httpd/conf.d/awstats.conf

line 30: add IP range you allow to access

    Require all granted

Comment out dòng 34

    [root@mail ~]# systemctl restart httpd

update reports manually ( it's updated automatically for hours by Cron )

    [root@mail ~]# /usr/share/awstats/wwwroot/cgi-bin/awstats.pl -update -config=mail.srv.world -configdir=/etc/awstats

    Create/Update database for config "/etc/awstats/awstats.mail.srv.world.conf" by AWStats version 7.4 (build 20150714)
    From data in log file "/usr/share/awstats/tools/maillogconvert.pl standard < /var/log/maillog |"...
    Phase 1 : First bypass old records, searching new record...
    Searching new records from beginning of log file...
    Phase 2 : Now process new records (Flush history on disk after 20000 hosts)...
    Jumped lines in file: 0
    Parsed lines in file: 5
    Found 1 dropped records,
    Found 0 comments,
    Found 0 blank records,
    Found 0 corrupted records,
    Found 0 old records,
    Found 4 new qualified records.

## Kết quả

Truy cập trang: 
http://mail.doanbadung.xyz/awstats/awstats.pl

![Imgur](https://i.imgur.com/O229FHI.png)

Ta thấy trong log có 1 mail vừa mới gửi