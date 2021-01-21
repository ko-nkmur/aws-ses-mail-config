# 補足情報
## 1 opendakim.confの設定について
変更箇所は以下の通りです。
### 1 Modeの変更(39行目付近)
変更前
  `Mode	v`
変更後
  `Mode sv`

### 2 KeyFileのコメントアウト(98行目付近)
変更前
  `KeyFile	/etc/opendkim/keys/default.private  `
変更後
  `# KeyFile	/etc/opendkim/keys/default.private  `

### 3 KeyTableのコメントアウトを外す(103行目付近)
変更前
  `# KeyTable	/etc/opendkim/KeyTable  `
変更後
  `KeyTable	/etc/opendkim/KeyTable  `

### 4 SigningTableのコメントアウトを外す(108行目付近)
変更前
  `# SigningTable	refile:/etc/opendkim/SigningTable  `
変更後
  `SigningTable	refile:/etc/opendkim/SigningTable  `

### 5 ExternalIgnoreListのコメントアウトを外す(112行目付近)
変更前
  `# ExternalIgnoreList	refile:/etc/opendkim/TrustedHosts  `
変更後
  `ExternalIgnoreList	refile:/etc/opendkim/TrustedHosts  `

### 6 InternalHostsのコメントアウトを外す(115行目付近)
変更前
  `# InternalHosts	refile:/etc/opendkim/TrustedHosts  `
変更後
  `InternalHosts	refile:/etc/opendkim/TrustedHosts  `

## 2 KeyTableの書式
書式  
  `[セレクタ名]._domainkey.[ドメイン名] [ドメイン名]:[セレクタ名]:[秘密鍵へのパス]  `
サンプルには以下のように設定しています。ドメイン名を【mikumiku3939.com】、セレクタ名を【20210101】
とした例です。最終行へ追記します。

設定例  
  `20210101._domainkey.mikumiku3939.com mikumiku3939.com:20210101:/etc/opendkim/keys/mikumiku3939/20210101.private  `

## 3 SigningTableの書式
書式
  `*@[ドメイン名] ._domainkey.[ドメイン名]  `

サンプルには以下のように設定しています。ドメイン名を【mikumiku3939.com】、セレクタ名を【20210101】
とした例です。最終行へ追記します。

設定例
  `*@mikumiku3939.com 20210101._domainkey.mikumiku3939.com  `

## 4 main.cfの設定について
ドメイン名を【mikumiku3939.com】とした設定例です。
変更箇所は以下の通りです。
### 1 ホスト名の変更(myhostnameの変更 75行目付近)
設定例
  `myhostname = mail.mikumiku3939.com  `

### 2 ドメイン名の変更(mydomainの変更 83行目付近)
設定例
  `mydomain = mikumiku3939.com  `

### 3 メールリレー設定の確認(inet_interfacesの設定確認 116行目付近)
  `【inet_interfaces = localhost】  `となっていることを確認してください。

### 4 IPv4の設定(inet_protocolsの変更 119行目付近)
IPv4とIPv6両方が有効になっていますと、Eメール送信ができないことがあります。
IPv4のアドレスで設定している場合、IPv4で設定します。  

設定例
  `inet_protocols = ipv4  `

### 5 masquarade_domainsの追加設定
メールサーバのホスト名を隠蔽してEメールを送信する設定です。初期設定の状態ではないため、追記します。

設定例
  `masquerade_domains = mikumiku3939.com  `

### 6 メール暗号化設定の追加
メールの暗号化の方法は、自己証明書を発行して使う方法とSTARTTLSを使う方法があります。ここではSTARTTLSの設定を紹介します。
STARTLSは通信の暗号化方法の一つで、通常の通信プロトコルをSSL/TLSで暗号化された状態に切り替えます。これによりSMTP通信が暗号化されます。
初期設定の状態ではないため、追記します。

設定例
  `
#STARTTLS
smtp_tls_CAfile = /etc/ssl/certs/ca-bundle.crt
  `

### 7 OpenDKIM 設定の追加
OpenDKIM で秘密鍵と公開鍵を作成後、追記します。

設定例
  `
# DKIM
smtpd_milters = inet:127.0.0.1:8891
non_smtpd_milters = $smtpd_milters
milter_default_action = accept
  `

### 8 TLS設定の追加
SMTPセッションでTLSを有効化、メールヘッダにプロトコルや暗号に関する情報を追記、ログ出力の設定を行います。

設定例
  `
#tls
smtp_tls_security_level = may
smtpd_tls_security_level = may
smtpd_tls_received_header = yes
smtp_tls_loglevel = 1
  `
