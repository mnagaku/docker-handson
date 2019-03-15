# docker-handson
dockerについてのハンズオン用

----

## 事前準備（ハンズオンまでにやっておいて欲しいこと）

### 1：Docker ID を取得しておく

https://hub.docker.com/signup

### 2：Docker Desktop をインストールしておく

OSの要件：
Microsoft Windows 10 Professional または Enterprise 64-bit版
Apple Mac OS Sierra 10.12以上

ハードウェアの要件：
メモリは8GBでギリ動く感じです。16GBとかを推奨します。

Windowsの注意点：
Hyper-V が使われるので、BIOSで Virtualization Technology (VT-x) の有効化が必要です。
Hyper-V は VirtualBox など他の仮想化ソリューションと共存できないので、既に他の仮想化ソリューションを入れている場合は、Docker Desktop を諦めて、Docker 作業用の Linux 仮想マシン (Ubuntu 18.04 LTS もしくは Debian stretch を推奨) を作成することを推奨します。

上記の条件を確認して、インストールできる要件を満たしているなら、

https://www.docker.com/products/docker-desktop

からインストーラをダウンロードして、インストールしてください。

### 3：Linux 上に Docker 環境を作る

Docker Desktop のインストールができない場合、仮想マシン、クラウド上のインスタンス、物理マシン、などの何らかの方法でDocker 作業用の Linux マシンを確保して、その上に Docker 環境を作ります。

Docker 作業用の Linux マシンは、Ubuntu 18.04 LTS もしくは Debian stretch を推奨します。RedHat 系など、他のディストリビューションでも、技術的に問題はありませんが、Docker コンテナイメージの多くが、Debian 系をベースに作成されているので、Linux に慣れていない人の場合、コマンド体系を揃えた方が無難でしょう。

以下のドキュメントを参考にインストールしてください。

Get Docker CE for Ubuntu
https://docs.docker.com/install/linux/docker-ce/ubuntu/

Get Docker CE for Debian
https://docs.docker.com/install/linux/docker-ce/debian/

合わせて、クラスタ構築に便利な docker-compose をインストールしてください。

Install Docker Compose
https://docs.docker.com/compose/install/

因みに、docker-compose は Docker Desktop に同梱されています。

### 4：Play with Docker を使う

Docker Desktop もしくは Docker 作業用の Linux マシンを準備できなかった場合でも、Docker の開発元が提供する Play with Docker を使えば、Docker ID でログインして、自分用のお試し環境を4時間使うことができます。この環境は起動して4時間経過すると、自動的に削除されます。
Play with Docker は、EC2上に確保されたリソースで提供されますが、リソース量が固定で運用されており、時間帯によっては満員で使えない場合があります。
