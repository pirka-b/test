# S3 / Rails (Active Storage) を連携して画像を保存
1. [AWS ( IAMユーザー作成・S3バケット作成 )](#1-aws--iamユーザー作成s3バケット作成-)
1. [Rails設定 ( S3と連携させるための設定 )](#2-rails設定--s3と連携させるための設定-)<br>

\------------------------------------------------------------------------------------------------------------------------------------------------------<br>
<details><summary>【 参考リンク 】</summary>
 
 - [【Railsガイド】Active Storage の概要](https://railsguides.jp/active_storage_overview.html)
 - [　Rails5.2から追加された credentials.yml.encのキホン](https://qiita.com/NaokiIshimura/items/2a179f2ab910992c4d39)
 - [【Rails】秘匿情報を環境毎に管理することができるMulti Environment Credentialsについて初心者向けにまとめてみた](https://techtechmedia.com/multi-environment-credentials/)
 </details>
 
\------------------------------------------------------------------------------------------------------------------------------------------------------<br>


<br><br>
## 1. AWS ( IAMユーザー作成・S3バケット作成 )
■ IAMユーザ作成<br>
- 新たにS3管理用にIAMユーザーを作成
- アクセスキーID・シークレットアクセスキー取得<br>
( 補足：後の工程で上記認証情報を Rails で使用する )
- AmazonS3FullAccess 権限付与<br>
( ※実際の本番環境では必要な権限のみを許可するように制限させる )<br><br>

■ S3 - バケット作成　( ※上記ユーザーでバケット作成 )<br>
- バケット作成<br>
( バケット名 (一意の名前) / リージョン ( ※日本なら ap-northeast-1(東京) が無難 )
- 上記バケット情報の取得<br>
( 補足：後の工程で上記バケット情報を Rails で使用する )<br><br><br>


## 2. Rails設定 ( S3と連携させるための設定 )
■ gem `aws-sdk-s3` のインストール<br>
- `aws-sdk-s3` をインストール<br>
( ※今回はGemfiileに `gem "aws-sdk-s3", require: false` と記述があり、EC2の環境構築時に `bandle install` でインストール済 )
- インストールされているか確認<br>
```
 $ gem list | grep aws-sdk-s3
```

<br><br>
■ 前工程で取得したAWS認証情報を Rails秘匿情報の設定ファイル `credentials.yml.enc` に格納(記述)
- 現在いるディレクトリを確認
```
# /home/ec2-user/raisetech-live8-sample-app と表示されることを確認
$ pwd
```
- `credentials.yml.enc` の存在確認
```
$ ls -la /config
```
- 上記設定ファイルがある場合は削除<br>
( ※後工程でこのファイルを編集しようとするとエラーが出て編集できないため )<br>
( 補足：この時に新規生成された `master.key` は、 既存の `credentials.yml.enc` の復号ができない )
```
$ rm /config/credentials.yml.enc
```
- Rails秘匿情報の設定ファイル `credentials.yml.enc` の編集　( ※鍵の新規作成 )<br>
( ※下記コマンド実行時、 `credentials.yml.enc` がない場合は 新たに`credentials.yml.enc` と `master.key` が生成され編集可能となる )
```
$ EDITOR=vim rails credentials:edit

----------------------------------------------------
# 秘匿情報の設定ファイルにAWS認証情報を記述 (前工程で取得したAWS情報を格納)

aws:
  access_key_id: <アクセスキーID>
  secret_access_key: <シークレットアクセスキー>
  active_storage_bucket_name: <保存先のS3バケット名>
```

<br><br>
\------------------------------------------------------------------------------------------------------------------------------------------------------<br>
<details><summary>【 development環境用の Rails秘匿情報の設定ファイル (development.yml.enc) を使用する場合《 Multi Environment Credentials 》】</summary>
 
- 現在いるディレクトリを確認
```
# /home/ec2-user/raisetech-live8-sample-app と表示されることを確認
$ pwd
```
- `development.yml.enc` の存在確認
```
$ ls -la /config/credentials
```

- 上記設定ファイルがある場合は削除<br>
( ※後工程でこのファイルを編集しようとするとエラーが出て編集できないため )<br>
( 補足：この時に新規生成された `development.key` は、 既存の `development.yml.enc` の復号ができない )
```
$ rm /config/credentials/development.yml.enc
```
- Rails秘匿情報の設定ファイル `development.yml.enc` の編集　( ※鍵の新規作成 )<br>
( ※下記コマンド実行時、 `development.yml.enc` がない場合は 新たに`development.yml.enc` と `development.key` が生成され編集可能となる )
```
$ EDITOR=vim rails credentials:edit --environment development

----------------------------------------------------
# 秘匿情報の設定ファイルにAWS認証情報を記述 (前工程で取得したAWS情報を格納)

aws:
  access_key_id: <アクセスキーID>
  secret_access_key: <シークレットアクセスキー>
  active_storage_bucket_name: <保存先のS3バケット名>
```
</details>
 
\------------------------------------------------------------------------------------------------------------------------------------------------------<br>

 
<br><br>
■ Railsのストレージ設定/定義を確認・編集　( ※必要に応じて内容を編集/記述 )
- ストレージ設定ファイル `storage.yml` の内容を確認
```
$ less /config/storage.yml
$ vim /config/storage.yml
 
----------------------------------------------------
# 設定ファイル内の下記 記述箇所を確認・編集
 
# Use rails credentials:edit to set the AWS secrets (as aws:access_key_id|secret_access_key)
amazon:
 service: S3
 access_key_id: <%= Rails.application.credentials.dig(:aws, :access_key_id) %>             # Rails秘匿情報設定ファイルに記述されている認証情報を参照している
 secret_access_key: <%= Rails.application.credentials.dig(:aws, :secret_access_key) %>     # 〃
 region: ap-northeast-1                                                                    # S3バケット作成時に選択したリージョンを記述
 bucket: <%= Rails.application.credentials.dig(:aws, :active_storage_bucket_name) %>       # Rails秘匿情報設定ファイルに記述されている認証情報を参照している
```

 <br><br>
■ データの保存先の変更
 - `development.rb` を編集　( ※必要に応じて本番環境の `production.rb` も同様に編集 ) 
```
$ vim config/environments/development.rb
 
------------------------------------------
# 下記に記述変更 (保存先を :local から :amazon へ変更)
 
#config.active_storage.service = :local
config.active_storage.service = :amazon
```
<br><br>
■ Nginx / Unicorn 再起動後、設定ファイルが適切に反映されているか確認 (動作確認) 
