# コマンドラインから自分のGmailでメールを送るだけまとめ

### メールアドレスのエンコード
~~~
user:~$ echo "your-email@gmail.com" | openssl enc -e -base64
eW91ci1lbWFpbEBnbWFpbC5jb20K
~~~

### アプリパスワードのエンコード
gmailのログインパスじゃない。取得は[ここから](https://support.google.com/accounts/answer/185833)
~~~
user:~$ echo "your-app-password" | openssl enc -e -base64
eW91ci1hcHAtcGFzc3dvcmQK
~~~

### tlsでsmtp.gmail.com:587と通信
~~~
user:~$ openssl s_client -starttls smtp -crlf -connect smtp.gmail.com:587

# 省略)なんやかんやいっぱい出てくる

Start Time: 1470427717
Timeout   : 300 (sec)
Verify return code: 0 (ok)
---
250 SMTPUTF8
~~~

### とりあえずEHLO
~~~
EHLO localhost
250-smtp.gmail.com at your service, [xxx.xxx.xxx.xxx]
250-SIZE 35882577
250-8BITMIME
250-AUTH LOGIN PLAIN XOAUTH2 PLAIN-CLIENTTOKEN OAUTHBEARER XOAUTH
250-ENHANCEDSTATUSCODES
250-PIPELINING
250-CHUNKING
250 SMTPUTF8

~~~

### ログインしてからの
~~~
AUTH LOGIN ⏎ Enter
334 VXNlcm5hbWU6
eW91ci1lbWFpbEBnbWFpbC5jb20K ⏎ Enter # エンコードしたemail
334 UGFzc3dvcmQ6
eW91ci1hcHAtcGFzc3dvcmQK ⏎ Enter # エンコードしたappパスワード
235 2.7.0 Accepted
~~~

### メール作成、送信
~~~
mail from:<your-email@gmail.com> ⏎ Enter                 # from
250 2.1.0 OK n9sm30048354paz.13 - gsmtp
rcpt to:<send-to-email@example.com> ⏎ Enter              # to
250 2.1.5 OK n9sm30048354paz.13 - gsmtp
data ⏎ Enter                                             # data
354  Go ahead n9sm30048354paz.13 - gsmtp
subject :test ⏎ Enter
this is a test mail from command line ⏎ Enter
. ⏎ Enter                                                # finish & sent email
250 2.0.0 OK 1470427837 n9sm30048354paz.13 - gsmtp
quit ⏎ Enter                                             # exit 
221 2.0.0 closing connection n9sm30048354paz.13 - gsmtp
read:errno=0
~~~

### どうでもよいこと
> 334 VXNlcm5hbWU6

の"VXNlcm5hbWU6"はbase64エンコードされた"Username"
> 334 UGFzc3dvcmQ6

の"UGFzc3dvcmQ6"はbase64エンコードされた"Password"
~~~
user:~$ echo "VXNlcm5hbWU6" | openssl enc -d -base64 ⏎ Enter
Username
user:~$ echo "UGFzc3dvcmQ6" | openssl enc -d -base64 ⏎ Enter
Password
~~~


