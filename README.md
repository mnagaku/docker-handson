# docker-handson

dockerについてのハンズオン on AWS

----

## 事前準備

- Docker ID を取得しておく

https://hub.docker.com/signup

----

## 目次

[JupyterNotebook入門lite](./JupyterNotebook入門lite.ipynb)

[作業場所をAWS上に構築](./作業場所をAWS上に構築.ipynb)

[Docker基本操作](./Docker基本操作.ipynb)

[Dockerイメージの作成](./dockerfile-demo/Dockerイメージの作成.ipynb)

[docker-compose](./compose-demo/docker-compose.ipynb)

----

## うんちく

### 1：歴史

Virtualization software  
https://en.wikipedia.org/wiki/Template:Virtualization_software

Dockerコンテナ時代の第一章の終わり、そして第二章の展望など  
https://www.publickey1.jp/blog/17/post_265.html

<img src=https://docs.docker.jp/_images/vm.png width=300/>

仮想マシン：ハードウェアを仮想化して、その上にOSを載せる
VMware Workstation(1999)、VMware ESX Server(2001)、Xen(2003)、VirtualBox(2007)、KVM(2007)

IaaS：仮想マシン基盤をクラウドサービス化
EC2(2006)、OpenStack(2010)

<img src=https://docs.docker.jp/_images/container.png width=300/>

コンテナ：OSを仮想化して、その中でプロセスを動かす
FreeBSD jail(2000)、Solaris Containers(2005)、LXC(2008)、Docker(2013)
※Dockerまでは、流行らなかった

コンテナオーケストレーションとそのサービス化：コンテナ基盤をクラウドサービス化
Kubernetes(2014)、Docker Swarm(2014)、ECS(2014)

![概念の進化](https://g.gravizo.com/svg?digraph%20G%20{仮想マシン->IaaS;仮想マシン->コンテナ;コンテナ->コンテナオーケストレーションとそのサービス化;IaaS->コンテナオーケストレーションとそのサービス化;})

21世紀に入ってからの20年、計算機における仮想化は、クラウド化とコンテナ化を軸に発展してきたと言えます。

### 2：なぜDockerは流行ったのか

* 当初のDockerは、LXC、cgroups(2008)、aufs(2006)を組み合わせて実現されたコンテナソリューションだったが、それ以前のコンテナソリューションに比べて、圧倒的に簡単に使えた
* ファイルシステムを扱う機能を取り込み、コンテナをイメージとしてパッケージできるようになったことで、使い捨てが可能となり、Immutable Infrastructureの有力な道具と見なされた
* 仮想マシンやクラウドに合わせた設計概念の整理が進み、OSではなくプロセスを中心に考えるようになった。その結果、仮想マシンより軽量なコンテナで十分なことが分かった
* モノリシックな構成は「単一責務の原則」に反しており、マイクロサービスまで考えると、単一責務（プロセス）を持つ、個々にはヘテロなコンテナ群によるシステム構成が理に適っていた

### 3：俺のとこでは動いた問題

近年のソフトウェアは、依存するモジュールが多いため、動作環境を揃えることが簡単ではありません。このため「俺のとこでは動いた問題」が発生しやすくなっています。これを防ぐためには、動作環境のパッケージングが有効です。

動かしたいプロセスが必要とする環境を用意したい、けれども、プロセス毎に要求が異なるので、それぞれのプロセスに合わせて分離した環境を用意したい、という場合、最も確実なのは、複数のマシンを用意することです。それぞれのマシンが動作環境のパッケージとして機能します。

#### 複数のマシンを用意する

* 物理でも良いし、仮想マシンでも良い
* (GOOD)マシン毎にOSを選べる
* (BAD)起動に時間がかかる

より手軽なコンテナでも動作環境のパッケージとして機能します。

#### コンテナ

* (GOOD)起動が速い
* (GOOD)使い捨てしやすい
* (BAD)OSのカーネルが共有なので選べない

動作環境を切り替える手法は、開発者によく使われています。

#### 環境の切り替え機構を使う

* alternatives(Linux)、Virtualenv(Python)など
* (GOOD)手元の開発環境を切り替えられる
* (BAD)統合的な動作環境パッケージにはならないので「俺のとこでは動いた問題」を解決できない

長久個人の見解としては、環境の切り替え機構は、「俺のとこでは動いた問題」を解決できないので、使うべきではないと考えています。現状、バランスが取れている解は、コンテナだと考えています。

### 4：コンテナの2つの使い方

コンテナを、軽量な仮想マシンと見なして、サーバプロセスをホストする使い方が、よく行われています。例えば、AWSのECSで、webアプリケーションのサーバを動かすような使い方です。

コンテナは、使い捨てしやすい、プロセスを動かすための環境と考えることができるので、特定の処理（実行コマンド）を、その動作環境といっしょにパッケージして、コマンドを動かすための道具にもできます。例えば、AWSのFargateで、バッチ処理を動かすような使い方です。コンテナベースのHPCも、こっちの使い方です。CI（ビルド、テスト）の基盤としても、こっちの使い方です。

### 5：（番外編）Docker以外のコンテナ環境

Dockerをサーバ用途で使う場合、サーバを制御する人が管理者権限を持っていても、運用上おかしくはありませんが、Dockerをコマンド用途で使う場合、コマンドを使いたい人全員に管理者権限を与えるのは、運用上の問題となりえます。コマンド用途でコンテナを使うなど、コンテナ利用の適用範囲を広げるために、管理者権限を必要としないコンテナ技術が使われ始めています。

#### Rootless Docker

https://docs.docker.com/engine/security/rootless/

Dockerを管理者権限なしに使うための製品。前提条件を満たしていれば、インストールにも管理者権限は不要。

#### Podman

https://podman.io/

赤帽が推す、Dockerと[互換性の高い](https://opencontainers.org/)、管理者権限不要なコンテナ製品。

#### Singularity

https://sylabs.io/singularity/

GPGPUと親和性の高い、管理者権限不要なコンテナ製品。

----

## このハンズオンで使われているソフトウェア

[OperationHub](https://github.com/NII-cloud-operation/OperationHub)
：JupyterHubをベースに、LC4RI用に調整された、JupyterNotebookのマルチユーザ環境

[mnagaku/operation-notebook](https://hub.docker.com/repository/docker/mnagaku/operation-notebook)
：NIIクラウド運用チームが実務で使っているJupyterのコンテナイメージにsshカーネルなどを追加したもの

[sshカーネル](https://github.com/NII-cloud-operation/sshkernel)
：富士通研究所が開発した、リモートシェルを実現するカーネル

----

## 公式チュートリアル

[始めましょう（和訳）](https://docs.docker.jp/get-started/toc.html)

[Docker 101 Tutorial](https://www.docker.com/101-tutorial)の[和訳](https://qiita.com/Michinosuke/items/5778e0d9e9c04038903c)

----
