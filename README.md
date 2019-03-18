# docker-handson

dockerについてのハンズオン用

----

## 事前準備

### 1：Docker ID を取得しておく

https://hub.docker.com/signup

### 2-1：Docker Desktop をインストールしておく

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

### 2-2：Linux 上に Docker 環境を作る

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

### 2-3：Play with Docker を使う

Docker Desktop もしくは Docker 作業用の Linux マシンを準備できなかった場合でも、Docker の開発元が提供する Play with Docker を使えば、Docker ID でログインして、自分用のお試し環境を4時間使うことができます。この環境は起動して4時間経過すると、自動的に削除されます。
Play with Docker は、EC2上に確保されたリソースで提供されますが、リソース量が固定で運用されており、時間帯によっては満員で使えない場合があります。

<a href="http://play-with-docker.com?stack=https://raw.githubusercontent.com/mnagaku/docker-handson/master/docker-compose.yml"><img src="https://raw.githubusercontent.com/play-with-docker/stacks/master/assets/images/button.png" /></a>

----

## うんちく

### 1：歴史

Virtualization software  
https://en.wikipedia.org/wiki/Template:Virtualization_software

Dockerコンテナ時代の第一章の終わり、そして第二章の展望など  
https://www.publickey1.jp/blog/17/post_265.html

<img src=https://docs.docker.com/images/VM%402x.png width=300/>

仮想マシン：ハードウェアを仮想化して、その上にOSを載せる
VMware Workstation(1999)、VMware ESX Server(2001)、Xen(2003)、VirtualBox(2007)、KVM(2007)

IaaS：仮想マシン基盤をクラウドサービス化
EC2(2006)、OpenStack(2010)

<img src=https://docs.docker.com/images/Container%402x.png width=300/>

コンテナ：OSを仮想化して、その中でプロセスを動かす
FreeBSD jail(2000)、Solaris Containers(2005)、LXC(2008)、Docker(2013)
※Dockerまでは、流行らなかった

コンテナオーケストレーションとそのサービス化：コンテナ基盤をクラウドサービス化
Kubernetes(2014)、Docker Swarm(2014)、ECS(2014)

<img src='https://g.gravizo.com/svg?
 digraph G {
   仮想マシン -> IaaS;
   仮想マシン -> コンテナ;
   コンテナ -> コンテナオーケストレーションとそのサービス化;
   IaaS -> コンテナオーケストレーションとそのサービス化;
 }
'/>

21世紀に入ってからの20年、計算機における仮想化は、クラウド化とコンテナ化を軸に発展してきたと言えます。

### 2：なぜDockerは流行ったのか

* 当初のDockerは、LXC、cgroups(2008)、aufs(2006)を組み合わせて実現されたコンテナソリューションだったが、それ以前のコンテナソリューションに比べて、圧倒的に簡単に使えた
* ファイルシステムを扱う機能を取り込み、コンテナをイメージとしてパッケージできるようになったことで、使い捨てが可能となり、Immutable Infrastructureの有力な道具と見なされた
* 仮想マシンやクラウドに合わせた設計概念の整理が進み、OSではなくプロセスを中心に考えるようになった。その結果、仮想マシンより軽量なコンテナで十分なことが分かった
* モノリシックな構成は「単一責務の原則」に反しており、マイクロサービスまで考えると、単一責務（プロセス）を持つ、個々にはヘテロなコンテナ群によるシステム構成が理に適っていた

### 3：俺のとこでは動いた問題

近年のソフトウェアは、依存するモジュールが多いため、

動かしたいプロセスが必要とする環境を用意したい、けれども、プロセス毎に要求が異なるので、それぞれのプロセスに合わせて分離した環境を用意したい、という場合、最も確実なのは、複数のマシンを用意することです。

#### 複数のマシンを用意する

* 物理でも良いし、仮想マシンでも良い
* (GOOD)マシン毎にOSを選べる
* (BAD)起動に時間がかかる

#### コンテナ


#### 環境の切り替え機構を使う

* alternatives(Linux)、Virtualenv(Python)など

### 4：コンテナの2つの使い方









公式チュートリアルをやってみる

