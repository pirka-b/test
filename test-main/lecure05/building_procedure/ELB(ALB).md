# ELB (ALB) の構築・設定
1. [ELB (ALB) の作成](#1-elb-alb-の作成)
1. [EC2のセキュリティグループ設定変更 ( セキュリティ対策 )](#2-ec2のセキュリティグループ設定変更--セキュリティ対策-)
1. [ブラウザにて動作確認 ( エラー対処 )](#3-ブラウザにて動作確認-エラー対処-)
1. [ Nginx の設定変更](#4-nginx-の設定変更)
1. [エラー対処 ( Nginx起動不可 )](#5-エラー対処--nginx起動不可-)<br><br>


## 1. ELB (ALB) の作成
- ELB (ALB) 作成<br>
( 補足：ロードバランサ経由でEC2と通信をするため、サブネットは各AZのパブリックサブネットを選択 )<br>
![elb(alb)](../images/ELB(ALB)/elb(alb).png)<br>

- ターゲットグループの作成<br>
( 補足：パブリックサブネット上のEC2を選択 )<br>
![elb(alb)-tg](../images/ELB(ALB)/elb(alb)-tg.png)<br>

- ELB (ALB) ヘルスチェック確認<br>
( ※ `Unhealthy` の場合、EC2・RDS、EC2内の Nginx/Unicorn が起動しているか確認 )<br>

- ELB (ALB) 用のセキュリティグループを新規作成<br>
( 補足：次工程でEC2側のセキュリティグループの設定変更をする際に使用 )<br>
![elb(alb)-securitygroup](../images/ELB(ALB)/elb(alb)-securitygroup.png)<br><br><br>


## 2. EC2のセキュリティグループ設定変更 ( セキュリティ対策 )
- 今回作成したELB (ALB) からの `HTTP通信 (80番ポート)` のみを許可するように設定<br>
( ※上記で作成した ELB(ALB) のセキュリティグループをソースに設定 (下図参照) )<br>
![ec2-securitygroup](../images/ELB(ALB)/ec2-securitygroup.png)<br>
( 補足：`SSH通信 (22番ポート)` の許可設定は残す ( ※EC2のメンテナンス・設定変更時にログインできるようにするため ) )<br><br><br>


## 3. ブラウザにて動作確認　( エラー対処 )
- 上記設定後、ブラウザに `ELB(ALB)のDNS名` を入力して動作確認すると Rails のセキュリティエラーが発生する<br>
![browser_security_error](../images/ELB(ALB)/error_handling/browser_security_error.png)<br><br>

- エラー文より、下記対応を実施<br>
( Rails設定ファイル `/home/ec2-user/raisetech-live8-sample-app/config/environments/development.rb` に下記を追記 ( 下図参照 ) )<br>
```
# 設定ファイル内の末尾 end の一行上に、ブラウザのエラー画面に記載されている一文(下記)を追加
 config.hosts << "×××.elb.amazonaws.com"
```
　　![development_rb](../images/ELB(ALB)/error_handling/development_rb.png)<br><br>

- Nginx / Unicorn の再起動後、再度ブラウザ確認　( 正常画面の表示を確認 (※エラー解消) )<br>
![browser_check1](../images/ELB(ALB)/browser_check1.png)<br><br><br>


## 4. Nginx の設定変更
- Nginx設定ファイル `neginx.conf` or `○○.conf ` 内の、<br>
設定項目 `server_name ○○;` を EC2インスタンスのIPアドレスではなく、ELB(ALB)のDNS名に変更
```
#server_name < EIP or IPアドレス >;
server_name < ELB(ALB)のDNS名 >;
```
<br>
( ※補足：上記設定変更は実施せずとも 前工程のブラウザ動作確認時、エラー画面・エラー対応後の正常画面は表示された )<br>

<br><br>
## 5. エラー対処 ( Nginx起動不可 )
- 上記 `server_name` 変更後、Nginx起動でエラーが発生<br><br>
![nginx_error](../images/ELB(ALB)/error_handling/nginx_error.png)<br>
```
# エラー文
could not build server_names_hash, you should increase server_names_hash_bucket_size: 64
--------------------------------------------
# server_name が長すぎるという趣旨の内容
```

<br>

- 下記対応を実施　( Nginx設定ファイル `nginx.conf` の `http {}` 内に、下記を記述 )
```
#server_names_hash_bucket_size 64;
server_names_hash_bucket_size 128;
```
![nginx_error_resolution](../images/ELB(ALB)/error_handling/nginx_error_resolution.png)<br><br>

- 設定ファイルの追記後、Nginxが起動可能となった ( ※エラー解消 )
