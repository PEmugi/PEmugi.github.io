<!--
.. title: WSL2 の仮想ディスクを圧縮する
.. slug: wsl2-vdisk-compression
.. date: 2021-08-26 01:33:36 UTC+09:00
.. tags: WSL2, Windows 
.. category: 
.. link: 
.. description: 
.. type: text
-->

WSL2 使っていると、気が付いたらハードディスクの容量を圧迫していたなんてことがよくあります。

仮想マシン上で大きなファイルを作成したり、作業をしているうちに当然仮想ディスクのサイズは大きくなりますが、仮想マシン上でファイルを削除し他場合、仮想ディスク内で `df -h` などすれば容量が減っていることを確認できるのですが、実は仮想ディスクのファイルそのもののサイズは解放されません。

ここでは、肥大化した仮想ディスクのファイルのサイズを解放する方法を紹介します。

<!-- TEASER_END -->

## 目次

[TOC]

## 仮想ディスクの確認

WSL2 の仮想マシンのファイルは Microsoft Store からインストールされた Ubuntu であれば以下のパスにあります。

`C:\Users\{USERNAME}\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_xxxxxxxxx\LocalState\ext4.vhdx`

.vhdx は Windows の Hyper-V の仮想ディスクフォーマットなのかしら...  

!!! tips "Tips: Docker Desktop for Windows の場合"
    Docker Desktop の WSL2 バックエンドでも WSL2 の仮想マシンを使いますが、むしろ Docker 仮想マシンの肥大化のがやばいかもしれません。  
    イメージビルドの際のキャッシュとかイメージそのもので容量を食べますし。  

    Docker の WSL2 のディスクイメージは以下にありました。

    `C:\Users\{USERNAME}\AppData\Local\Docker\wsl`


### 私の環境の圧縮前のサイズはこんな感じ

3.6GB ぐらいでしょうか。  
![](/images/posts/wsl/wsl2-vdisk-compression-1.png)

`df -h` の結果は以下なので実際のディスク消費量と 1GB ぐらい差がありそうです。

![](/images/posts/wsl/wsl2-vdisk-compression-2.png)

## 仮想ディスクの圧縮

### 1. WSL の停止

WSL を止めます。  
仮想マシンを停止した後、念のため WSL のサービスも落とします。

#### 仮想マシンのシャットダウン

PowerShell か Command prompt で以下を実行

```powershell
wsl --shutdown
```

#### WSL サービスの停止

PowerShell か Command prompt を管理者として実行し、以下コマンドを実行

```shell
net stop LxssManager
LxssManager サービスを停止中です.
LxssManager サービスは正常に停止されました。
```

### 2. DISKPART で圧縮

PowerShell や Command prompt から以下コマンドで DISKPART を起動

```shell
diskpart
```

起動したら以下コマンドで仮想ディスクを選択します

```shell
DISKPART> select vdisk file="C:\Users\pemugi\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_xxxxxxxxx\LocalState\ext4.vhdx"

DiskPart により、仮想ディスク ファイルが選択されました。
```

そして圧縮します

```shell
DISKPART> compact vdisk

  100% 完了しました
DISKPART> exit
```

### 3. WSL サービスの再開

PowerShell か Command prompt を管理者として実行し、以下コマンドを実行

```shell
net start LxssManager
```

## まとめ

DISKPART を使って vhdx を圧縮する方法を紹介しました。  
Webエンジニアやバックエンドエンジニアの開発環境は、ここしばらく Mac が優勢ですが、今後 Windows + WSL2 な人も増えてきそうかな？

WSL や Docker Desktop for Windows (WSL2) を使ってて、仮想ディスクの容量に困ったら試してみてくださいませ～。
