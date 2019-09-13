# 「Linuxのしくみ」での学びを残す
## 環境
vmでなく実機上のシステムが推奨されており、
それを踏まえるとrasberrypiしかなかった。
（もう一台Linux搭載のPCあるが動作が不安定だった...謎）

- ハードウェア
  - rasberrypi zero
- os
  - Raspbian GNU/Linux 10
  
## 1.コンピュータシステ
- コンピュータシステムがどうさするときのハードウェアでのおおよその動作
1. 入力デバイス、あるいはネットワークアダプタを介してコンピュータに何らかの処理を依頼
2. メモリ上に存在する命令の読み出し、CPUへの配置と実行、結果をメモリ上のデータを保持する領域で保持
3. メモリ上のデータをHDDやSSDなどのストレージデバイスに書き込む or ネットワークを介して別のコンピュータに転送 or 出力デバイスを介して人間に表示
4. 1.へ戻る（繰り返す）

この手順を繰り返して１つの処理をまとめたものをプログラムと呼ぶ。

- プログラムの種類
  - アプリケーション：ユーザーに直接役に立つ。そのまんま。（ネイティブアプリっぽい）
  - ミドルウェア：多くのアプリケーションに共通した処理を切り出してアプリケーションの手助けをする。（web server, DB system）
  - OS：ハードウェアを直接操作して、アプリケーションやミドルウェアの実行に必要な機能を提供。（Linux）
## 2.ユーザモードで実現する機能

## 3. プロセス管理
