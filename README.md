# Setting up the LAMP on CentOS7 with Ansible
CentOS7.xにコマンド一発でLAMP環境を構築します。

```shellsession
$ ansible-playbook site.yml
```
```shellsession
$ ansible-playbook -i inventories/production site.yml
```

* AWSの[Amazon Lightsail](https://aws.amazon.com/jp/lightsail/)上のCentOS7.8で動作確認をしています。


## インストール内容
1. OS
    * SELinux (disable)
    * 日本時間化
    * スワップの作成 (デフォルトはOFF)
    * yum-cron
    * git
1. Apache 2.4
    * HTTPヘッダーの基本設定
    * VirtualHost
    * 自動起動
1. PHP 7.4
    * `php.ini`の基本設定
    * composer
1. MySQL 5.7
    * my.cnfの基本設定
    * rootのパスワード変更
    * 自動起動
1. memcached
    * `/etc/sysconfig/memcached`の基本設定
    * PHPモジュールのインストール
    * 自動起動

### 備考
* memcachedはデフォルトではインストールされません。`inventories/(環境)/group_vars/webservers`で設定を変更できます。

## 設定
実行前に以下の設定を行ってください。

### インスタンスの作成
LAMP環境を構築したいインスタンスを適当に作成し、SSHでログインできるよう設定してください。（パスワードの入力はOFF）

### Ansibleの実行環境
お使いの端末にAnsibleをインストールしてください。
https://docs.ansible.com/ansible/2.9_ja/installation_guide/intro_installation.html

### 設定ファイルの変更
最低限の設定内容を記載します。

#### inventories/(環境)/hosts
接続情報を設定します。

適当なテキストエディタで開き、作成したインスタンスのIPアドレスやドメインを記入してください。また必要があればSSHのポートやログインするユーザーも変更してください。

```ini
[webservers]
example.com

[dbservers]
example.com

[all:vars]
ansible_port=22
ansible_ssh_user=centos
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python
```

#### inventories/(環境)/group_vars/webserver
Apacheの設定項目をYAML形式で入力します。

```yaml
create_swap: false

apache:
  port: 80
  servername: example.com
  documentroot:
    path: /web/public
    owner: root
    group: root
```

#### inventories/(環境)/group_vars/dbserver
MySQLの設定項目をYAML形式で入力します。

```yaml
create_swap: false

mysql:
  root_host: localhost
  root_pw: "6AtKCey8sDehPQxnkshyjhThx3HqhPEz"
  slowquery:
    path: /var/log/mysqld_slowquery.log
```

## Licence
The MIT License  
Copyright 2020 M.Katsube <katsubemakito@gmail.com>
