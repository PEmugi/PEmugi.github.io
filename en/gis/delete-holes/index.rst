.. title: [QGIS] ポリゴンの小さい穴や隙間を削除する
.. slug: delete-holes
.. date: 2021-09-19 18:53:30 UTC+09:00
.. tags: GIS, QGIS
.. category: 
.. link: 
.. description: 
.. type: text

はじめに
======================

Dissolve などを使ってポリゴンを結合していくとこんな感じで隙間が開いちゃうことってありますよね。

.. image:: /images/posts/gis/polygon-with-small-hole-gap.png
    :height: 300px

この穴をふさいでみましょう。

.. TEASER_END

.. contents::

手順
=============

プロセシングツールボックスから「孔 (hole) の削除」ツールを開きます。

.. figure:: /images/posts/gis/qgis-delete-hole-tool-button.png

後は、入力レイヤを設定して「実行」するだけです。

.. figure:: /images/posts/gis/qgis-delete-hole-tool-window.png
    :height: 400px

.. HINT::
    穴の面積を指定したい場合は「この面積より小さな孔を削除」に値を入れます。


結果
------------

こんな感じできれいになりました。

.. figure:: /images/posts/gis/polygon-clean-hole-gap.png
    :height: 300px
