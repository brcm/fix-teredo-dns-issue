# リモートアシスタンス【teredoで「サーバー名を解決できませんでした」のエラー】

これまでリモートアシスタンスを使って遠隔地と毎日1回はデスクトップの共有を行っていたのですが、2015年の5月初週ぐらいから急に接続ができなくなりました。

原因は、dnsが名前解決してくれないことのよう。


環境はアシスタンスを受ける側とする側ともにwindows 7

リモートアシスタンスはNAT越えにipv6トンネリング技術の１つteredoを利用する。
ネットワークの設定を見ても、ipv6も有効にしているし、インターネットにも接続できている。


アシスタンスを受ける側のPCのコマンドプロンプト（管理者として実行）で
```
netsh interface ipv6 show teredo
```
とやると

エラー：サーバー名を解決できませんでした
と表示される。
以前までは普通にteredo動いていたんだけど、どういう原因かわからないが、windows7デフォルトで指定のあるteredoサーバ(teredo.ipv6.microsoft.com.)の名前解決が死んでいる模様。

pingを打ってみるも
```
ping teredo.ipv6.microsoft.com
```
ping要求ではホスト teredo.ipv6.microsoft.com が見つかりませんでした。ホスト名を確認してもう一度実行してください。
DNSサーバを変えてみるもダメ...
ネットワークアダプタの再起動、PCの再起動、teredoの再起動といろいろやってみましたが状況が変わりませんでした。
社内のwindows7のPCにおいてはまさに全PCそんな状況でした。
途方に暮れかけそうになったのですが、幸い1台だけwindows8.1でテストしていて、そこで
```
netsh interface ipv6 show teredo
```
とやるとこっちは正常にteredoが動いていて、なんでかなと眺めてみるとteredoサーバがwindows7のものと違っていました。
windows8.1のteredoサーバは「win8.ipv6.microsoft.com」。

win8からデフォルトで違うサーバを見ているようです。

もしかしてこのサーバ使えばいけんじゃないかと思って、ためしにwindows7側のteredoサーバを
```
netsh interface ipv6 set teredo type=client win8.ipv6.microsoft.com
```
として変更し、OKが表示されたら再度
```
netsh interface ipv6 show teredo
```
をやるとアシスタンスを受ける側のteredoが復活しました。

アシスタンスを行う側も同様にteredoサーバをwin8...のものを利用し、ようやくリモートアシスタンスが復活しました。

このままwin8サーバを使い続けていいのかどうかはよくわかりませんが、当面これでしのげればいいです。

hiroshi akutsu (id:hakoniwahaniwa) 1年前
