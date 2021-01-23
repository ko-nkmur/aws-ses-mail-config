# 補足情報<br>
## 1 opendkim.confの設定について
変更箇所は以下の通りです。<br>
### 1 Modeの変更(39行目付近)
変更前<br>
`Mode	v`<br>
変更後<br>
`Mode sv`<br>

### 2 KeyFileのコメントアウト(98行目付近)
変更前<br>
`KeyFile	/etc/opendkim/keys/default.private  `<br>
変更後<br>
`# KeyFile	/etc/opendkim/keys/default.private  `<br>

### 3 KeyTableのコメントアウトを外す(103行目付近)
変更前<br>
`# KeyTable	/etc/opendkim/KeyTable  `<br>
変更後<br>
`KeyTable	/etc/opendkim/KeyTable  `<br>

### 4 SigningTableのコメントアウトを外す(108行目付近)
変更前<br>
`# SigningTable	refile:/etc/opendkim/SigningTable  `<br>
変更後<br>
`SigningTable	refile:/etc/opendkim/SigningTable  `<br>

### 5 ExternalIgnoreListのコメントアウトを外す(112行目付近)
変更前<br>
`# ExternalIgnoreList	refile:/etc/opendkim/TrustedHosts  `<br>
変更後<br>
`ExternalIgnoreList	refile:/etc/opendkim/TrustedHosts  `<br>

### 6 InternalHostsのコメントアウトを外す(115行目付近)
変更前<br>
`# InternalHosts	refile:/etc/opendkim/TrustedHosts  `<br>
変更後<br>
`InternalHosts	refile:/etc/opendkim/TrustedHosts  `<br>

## 2 KeyTableの書式
書式<br>
`[セレクタ名]._domainkey.[ドメイン名] [ドメイン名]:[セレクタ名]:[秘密鍵へのパス]  `<br>

サンプルには以下のように設定しています。ドメイン名を【mikumiku3939.com】、<br>
セレクタ名を【20210101】とした例です。最終行へ追記します。<br>
設定例<br>
`20210101._domainkey.mikumiku3939.com mikumiku3939.com:20210101:/etc/opendkim/keys/mikumiku3939/20210101.private  `<br>

## 3 SigningTableの書式
書式<br>
`*@[ドメイン名] ._domainkey.[ドメイン名]  `<br>

サンプルには以下のように設定しています。ドメイン名を【mikumiku3939.com】とした例です。<br>
最終行へ追記します。<br>
設定例<br>
`*@mikumiku3939.com 20210101._domainkey.mikumiku3939.com  `<br>

## 4 main.cfの設定について
ドメイン名を【mikumiku3939.com】とした設定例です。<br>
変更箇所は以下の通りです。<br>
### 1 ホスト名の変更(myhostnameの変更 75行目付近)
設定例<br>
`myhostname = mail.mikumiku3939.com  `<br>

### 2 ドメイン名の変更(mydomainの変更 83行目付近)
設定例<br>
`mydomain = mikumiku3939.com  `<br>

### 3 メールリレー設定の確認(inet_interfacesの設定確認 116行目付近)
`【inet_interfaces = localhost】  `となっていることを確認してください。<br>

### 4 IPv4の設定(inet_protocolsの変更 119行目付近)
IPv4とIPv6両方が有効になっていますと、Eメール送信ができないことがあります。<br>
IPv4のアドレスで設定している場合、IPv4で設定します。<br>
設定例<br>
`inet_protocols = ipv4  `<br>

### 5 masquarade_domainsの追加設定
メールサーバのホスト名を隠蔽してEメールを送信する設定です。初期設定の状態ではないため、<br>
追記します。<br>
設定例<br>
`masquerade_domains = mikumiku3939.com  `<br>

### 6 メール暗号化設定の追加
メールの暗号化の方法は、自己証明書を発行して使う方法とSTARTTLSを使う方法があります。<br>
ここではSTARTTLSの設定を紹介します。<br>
STARTTLSは通信の暗号化方法の一つで、通常の通信プロトコルをSSL/TLSで暗号化された状態に切り替えます。これによりSMTP通信が暗号化されます。<br>
初期設定の状態ではないため、追記します。<br>
設定例<br>
`#STARTTLS`<br>
`smtp_tls_CAfile = /etc/ssl/certs/ca-bundle.crt`<br>

### 7 OpenDKIM 設定の追加
OpenDKIM で秘密鍵と公開鍵を作成後、追記します。<br>
設定例<br>
`# DKIM`<br>
`smtpd_milters = inet:127.0.0.1:8891`<br>
`non_smtpd_milters = $smtpd_milters`<br>
`milter_default_action = accept`<br>

### 8 TLS設定の追加
SMTPセッションでTLSを有効化、メールヘッダにプロトコルや暗号に関する情報を追記、<br>
ログ出力の設定を行います。<br>
設定例<br>
  `#tls`<br>
  `smtp_tls_security_level = may`<br>
  `smtpd_tls_security_level = may`<br>
  `smtpd_tls_received_header = yes`<br>
  `smtp_tls_loglevel = 1`<br>
