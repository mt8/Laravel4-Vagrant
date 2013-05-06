# Laravel + Vagrant

Ubuntu 12.04 Vagrant + [Laravel4](http://four.laravel.com)+ PHP 5.4.

このリポジトリはGithubの[bryannielsen/Laravel4-Vagrant](https://github.com/bryannielsen/Laravel4-Vagrant)の日本語フォークです。

## 動作要件

ホストマシンで必要なソフトウェアです。

* VirtualBox - フリーな仮想化ソフトウェア [ダウンロード](https://www.virtualbox.org/wiki/Downloads)
* Vagrant - Virtualbox イメージ操作ツール [Vagrantトップページ](https://www.vagrantup.com) Downloadボタンをクリックしてください
* Git - ソース管理 [ダウンロード](http://git-scm.com/downloads)

## 準備


* このリポジトリをクローンします。`git clone git://github.com/HiroKws/Laravel4-Vagrant.git`
* 新しく生成されたディレクトリーに移動し、`vagrant up`を実行します。
* 最初に実行する時、Vagrantは仮想Boxイメージを取得する必要があります。転送速度は~300mb程度ですので、ダウンロード速度により多少の時間はかかります。
* 続いてVagrantはパペットを使い、基本的な仮想Boxに対しLAMP関係の設定をします。(数分かかります。)、それからComposerのapp中のcomposer.jsonで定義されている全てのパッケージを取得する必要がありますので、最初の準備ではもう少し時間がかかります。
* 全て上手く行ったかどうか確認するためには、ブラウザでhttp:://localhost:8888へアクセスしてください。

*注意：多分ホスト側のOSでwww/app/storageフォルダーのパーミッションを777へ変更する必要があります。*


例えば：`chmod -R 777 www/app/storage/`

*追加(by HiroKws)* 'vagrant up'実行中に、パッケージの取得に失敗する場合、生成されたVagrantfileをエディターで開き、一部を以下のように修正してください。

    lv4_config.vm.provision :shell, :inline => "echo \"America/New_York\" | sudo tee /etc/timezone && dpkg-reconfigure --frontend noninteractive tzdata"
    lv4_config.vm.provision :shell, :inline => "apt-get update --fix-missing"
    lv4_config.vm.provision :puppet do |puppet|

真ん中の行、apt-get updateを実行する一行を加えます。（５月６日、Twitterにより作者の方に確認しました。）


## 使用法

VagrantのBoxに関係した基本的な設定情報です。

### ポートフォワード

* 8888 - Apache
* 8889 - MySQL
* 5433 - PostgreSQL


### デフォルトMySQL/PostgreSQLデータベース

* User: root
* Password: root
* DB Name: database


### PHP XDebug

XDebugはビルドに含まれていますが、 **デフォルトでは使用可能にしていません。** パフォーマンスに影響を与えるためです。

#### XDebugを有効にする

1. `puppet/manifests/phpbase.pp`の先頭を`$use_xdebug = "1"`に設定する
2. 続いて`vagrant up`でBoxの準備を行います。もし、既に準備済みであれば`vagrant provision`コマンドを使用してください。
3. これで、 **9001ポート** でXDebugが使用できます。

**XDebugツール**

* [SublimeXDebug](https://github.com/Kindari/SublimeXdebug) - 無料のSublimeTextプラグイン
* [MacGDBP](http://www.bluestatic.org/software/macgdbp/) - 無料のMac OSXツール
* [Codebug](http://www.codebugapp.com/) - 有料のMac OSXツール


_注目：全XDebugの設定は`puppet/modules/php/templates/php.ini.erb`の中のphp.iniテンプレートで変更可能です。_

### Vagrant

Vagrantには[英語で書かれた素晴らしいドキュメント](http://vagrantup.com/v1/docs/index.html) がありますが、いくつかのコマンドを紹介します。

* `vagrant up` 仮想マシンを開始し準備する
* `vagrant suspend` 基本的にマシンを'sleep'状態にします。バックアップを利用し再開するには`vagrant resume`を使用します。
* `vagrant halt` マシンをシャットダウンします。再開には`vagrant up`を実行します。
* `vagrant ssh` 仮想マシンへシェルアクセスします。

----
##### 仮想マシンのスペック #####

* OS     - Ubuntu 12.04
* Apache - 2.2.22
* PHP    - 5.4.14
* MySQL  - 5.5.24
* PostgreSQL - 9.1
* Beanstalkd - 1.4.6
* Redis - 2.2.12
* Memcached - 1.4.13
