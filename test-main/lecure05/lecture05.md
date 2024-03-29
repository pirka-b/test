# 【 lecture05 】
 **- Ruby on Rails サンプルアプリケーションのデプロイ/ ELB(ALB) / S3 / 構成図 -**<br><br>


## ■ 課題内容 (目次)
1. [EC2上にサンプルアプリケーションをデプロイ・動作確認](#1-ec2上にサンプルアプリケーションをデプロイ動作確認)<br>
　1-1. [組み込みサーバー (Puma) のみでデプロイ・動作確認](#1-1-組み込みサーバー-Puma-のみでデプロイ動作確認)<br>
　1-2. [サーバアプリケーションを分けて (Nginx/Unicorn) デプロイ・動作確認](#1-2-サーバアプリケーションを分けて-nginxunicorn-デプロイ動作確認)
2. [ELB (ALB) を追加・動作確認](#2-elb-alb-を追加動作確認)
3. [S3 を追加・動作確認 ( S3 / Rails (Active Storage ) を連携)](#3-s3-を追加動作確認--s3--rails-active-storage--を連携)
4. [構成図作成　( 上記環境の構成図書き起こし )](#4-構成図作成-上記環境の構成図書き起こし-)<br>

　( ※今回使用するサンプルアプリケーションは第3回講義 (Lecture03) のものを使用)<br>
 
--- 

<br><br>
## 1. EC2上にサンプルアプリケーションをデプロイ・動作確認

### 1-1. 組み込みサーバー (Puma) のみでデプロイ・動作確認
- [EC2_eivironment_deploy.md](./building_procedure/EC2_eivironment_deploy.md) を元に環境構築、サンプルアプリケーションのデプロイ/起動を実施
- 起動後、ブラウザにて動作確認
- RDS( DB:MySQL )へ接続し、データ登録状況も確認<br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ サンプルアプリケーションの動作環境確認<br>
| 動作環境 | バージョン | 
| -------- | ---------- |  
| Ruby     | 3.1.2      |  
| Bundler  | 2.3.14     |  
| Rails    | 7.0.4      |  
| Node     | v17.9.1    |  
| Yarn     | 1.22.19    | 

( 環境構築後、各種バージョン確認を実施 )<br>
![サンプルアプリケーションの動作環境確認](./images/EC2_environment_deploy/version_check.png)<br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ ブラウザ動作確認 `ポート3000番`<br>
　( サンプルアプリケーション起動後、ブラウザにて接続確認 ( ※下図はブラウザよりデータ登録 (テキスト/画像) 後の画面 ) )<br>
![ブラウザ動作確認(Puma)](./images/EC2_environment_deploy/browser_check.png)<br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ RDS ( DB:MySQL ) のデータ登録確認<br>
![RDS( DB:MySQL )のデータ登録確認](./images/EC2_environment_deploy/rds_mysql_check.png)<br><br>


--- 

### 1-2. サーバアプリケーションを分けて (Nginx/Unicorn) デプロイ・動作確認
- [Web-Nginx_AP-Unicorn.md](./building_procedure/Web-Nginx_AP-Unicorn.md) を元に、Webサーバ (Nginx) / APサーバ (Unicorn) に分けてサンプルアプリケーションのデプロイ/起動を実施
- 起動後、ブラウザにて動作確認
- RDS( DB:MySQL )へ接続し、データ登録状況も確認<br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ ブラウザ動作確認 `ポート80番` <br>
　( サンプルアプリケーション起動後、ブラウザにて接続確認 ( ※下図はブラウザよりデータ登録 (テキスト/画像) 後の画面 ) )<br>
![ブラウザ動作確認(Nginx/Unicorn)](./images/Web-Nginx_AP-Unicorn/browser_check.png)<br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ RDS ( DB:MySQL ) のデータ登録確認<br>
![RDS( DB:MySQL )のデータ登録確認](./images/Web-Nginx_AP-Unicorn/rds_mysql_check.png)<br><br>


--- 

## 2. ELB (ALB) を追加・動作確認
- [ELB(ALB).md](./building_procedure/ELB(ALB).md) を元に ELB (ALB) の構築・設定、動作確認を実施
- 追加後、ブラウザにて動作確認
- RDS( DB:MySQL )へ接続し、データ登録状況も確認<br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ ブラウザ動作確認<br>
　( ELB (ALB) の構築・設定 後、ブラウザにて接続確認 ( ※下図はブラウザよりデータ登録 (テキスト/画像) 後の画面 ) )<br>
![ブラウザ動作確認(ELB(ALB))](./images/ELB(ALB)/browser_check2.png)<br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ RDS ( DB:MySQL ) のデータ登録確認<br>
![RDS( DB:MySQL )のデータ登録確認](./images/ELB(ALB)/rds_mysql_check.png)<br><br>


--- 

## 3. S3 を追加・動作確認 ( S3 / Rails (Active Storage ) を連携)
- [S3_Rails(ActiveStorage).md](./building_procedure/S3_Rails(ActiveStorage).md) を元に S3 / Rails(ActiveStorage) を連携、動作確認を実施
- 連携後、ブラウザにて動作確認
- RDS( DB:MySQL )へ接続し、データ登録状況も確認<br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ ブラウザ動作確認<br>
　(  S3 / Rails(ActiveStorage) を連携後、ブラウザにて接続確認 ( ※下図はブラウザよりデータ登録 (テキスト/画像) 後の画面 ) )<br>
![ブラウザ動作確認1(S3/Rails(ActiveStorage))](./images/S3_Rails(ActiveStorage)/browser_check1.png)<br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ S3バケットの画像保存確認<br>
![S3バケットの画像保存確認1](./images/S3_Rails(ActiveStorage)/s3_save_check1.png)<br><br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ RDS ( DB:MySQL ) のデータ登録確認<br>
![RDS( DB:MySQL )のデータ登録確認1](./images/S3_Rails(ActiveStorage)/rds_mysql_check1.png)<br><br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
<details><summary>【 既存データ更新の動作確認も実施 ( ※テーブル内最下段より2つ目 [apple-S3] を更新 ) 】</summary>
   
\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ ブラウザ動作確認<br>
![ブラウザ動作確認2(S3/Rails(ActiveStorage))](./images/S3_Rails(ActiveStorage)/browser_check2.png)<br>
   
\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ S3バケットの画像保存確認<br>
![S3バケットの画像保存確認2](./images/S3_Rails(ActiveStorage)/s3_save_check2.png)<br><br>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>
■ RDS ( DB:MySQL ) のデータ登録確認<br>
![RDS( DB:MySQL )のデータ登録確認2](./images/S3_Rails(ActiveStorage)/rds_mysql_check2.png)<br>
   
</details>

\-----------------------------------------------------------------------------------------------------------------------------------------<br>

<br><br>

--- 

## 4. 構成図作成　( 上記環境の構成図書き起こし )
![構成図](./images/Diagram/diagram_lecture05.png)
