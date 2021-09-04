.. title: Docker Desktop for Windows のデータを別のドライブに移動する
.. slug: wsl2-move-docker-vdisk
.. date: 2021-09-05 01:44:16 UTC+09:00
.. tags: WSL2, Docker, Windows
.. category: 
.. link: 
.. description: 
.. type: text


| WSL2 と連携させて Docker Desktop for Windows を使用していますが、RDBMS のコンテナなどを使用していると、結構な頻度で IO が発生します。  
| なので、なるべくシステムディスクとは分けておきたいのですが、Docker Desktop for Windows 用の WSL はデフォルトだとシステムディスク上に作成されます。
| ここでは、Docker Desktop for Windows のデータ格納用の WSL である docker-desktop-data をほかのドライブに移動する方法を紹介します。

.. TEASER_END

.. contents::

Docker Desktop for Windows の WSL
============================================

Docker Desktop for Windows をインストールすると以下2つのWSL2ディストリビューションが作られます。

- docker-desktop
    Docker 関連のサービスを実行しているディストリビューション

- docker-desktop-data
    コンテナイメージやボリュームなどのデータを格納しているディストリビューション

| RDBMS などを運用していると、docker-desktop-data 内のボリュームに対して IO が発生するわけです。
| 別ドライブのディレクトリを bind mount すれば IO を別ディスクに逃がせますが、WSL2 から Windows のファイルシステムに対する IO 性能はあまり出ないようです。実際遅かった。

docker-desktop-data の移動
========================================

というわけで docker-desktop-data を別のドライブに移動していきましょう。

docker-desktop-data の仮想ディスク(.vhdx) はデフォルトでは以下に配置されます。

::

    %LOCALAPPDATA%\Local\Docker\wsl\data\ext4.vhdx

これを D: の適当なディレクトリに移動していきます。

1. WSL の停止
-------------------

WSL が稼働していると移動できないので、まずは WSL を止めます。

PowerShell か CMD で以下実行

.. code:: bash

    wsl --shutdown

2. docker-desktop-data のエクスポート
-------------------------------------------

ディストリビューションを tar アーカイブにエクスポートします。

再びインポートしたら不要なので、適当な場所に出力して ok 。

PowerShell か　CMD で以下実行

.. code:: bash

    wsl --export docker-desktop-data D:\wsl\docker-desktop\docker-desktop-data.tar

3. docker-desktop-data を WSL から削除
-----------------------------------------------------

既存の docker-desktop-data を削除します。この時点でシステムディスク上の仮想イメージ(ext4.vhdx)は削除されます。

PowerShell か　CMD で以下実行

.. code:: bash

    wsl --unregister docker-desktop-data

4. docker-desktop-data を再インポート
--------------------------------------------

| 2 の手順でエクスポートした tar を指定して、再度 WSL に docker-desktop-data を登録します。
| 格納先に D: 以下のディレクトリを指定します。
| version 指定を忘れないように

PowerShell か　CMD で以下実行

.. code-block:: bash

    wsl --import docker-desktop-data D:\wsl\docker-desktop\data D:\wsl\docker-desktop\docker-desktop-data.tar --version 2

5. Docker Desktop の起動
------------------------------------

Docker Desktop を再起動して作業は終わりです。

2 で出力した tar は削除してかまいません。


おわり
===========

Docker による IO やシステムディスク容量への圧迫が気になる人は試してみてくださいねー。

全然 GIS のこと書いてねぇな...
