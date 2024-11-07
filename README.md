# 自宅Webサーバーを立てる方法
## サーバーを準備
①サーバーに使うPCのIPを固定します

②PCにサーバーソフトをインストールします
Apache(httpd)がおすすめ
```
sudo apt install apache2
```
でインストール

③別の端末でサーバーを立てたPCにアクセスしてみましょう(何もしなくても何らかのサイトがすでにデフォであります)

起動していない可能性があるので
```
sudo apachectl start
```
で起動できます
htmlファイルはおそらく`/var/www/html`の中にあります(Linuxの場合)

アクセスできたら次にすすむ

## 公開事前準備
①ルータの管理画面で外部からのポート(デフォルトでhttpは80、httpsは443を)サーバーPCのIPに転送するようにしましょう

②自分の[グローバルIPを確認](https://www.cman.jp/network/support/go_access.cgi) を確認します

③スマートフォンのWiFiをOFFにして(モバイルデータ通信で)確認したグローバルIPにアクセスしてみましょう
アクセスできたら次にすすむ

## ドメイン
①[MyDNS](https://www.mydns.jp/) にアクセスしてドメインを撮ります。
画面上部に記載のあるFree Sub-Domain Listから選択したドメインについては無料で使用可能です←おすすめ

②DDNS(自宅サーバーの場合)を使うので自身のIPグローバルをMyDNS等に送信する必要があります。Winなら[DiCE](https://forest.watch.impress.co.jp/library/software/dice/) を使うのがおすすめ

Linuxの場合は
```
sudo apt install curl
```
でCURLをインストールし、
```python
import os
import time
userid = "ここにユーザーIP(mydnsXXXXXXってやつ)"
password = "ここにパスワード"
while True:
    os.system(f"curl -u {userid}:{password} https://www.mydns.jp/login.html")
    print('updated')
    time.sleep(7200)
```
見てわかるように永遠に繰り返すのでずっと起動しておいてください

③アクセスできるようになるまで待つ(最大で1週間ほどかかります)
エラーが起きているかどうかは
```
nslookup URL

例:
nslookup suken.daemon.asia
```
返ってきた値が自分のグローバルIPと等しい場合はドメインの取得がうまくいっています

## https化する
①Apacheのポートを80から別のものに変更し、Apacheを再起動
```
sudo apachectl restart
```
②Nginxをインストールします
```
sudo apt install nginx
```
③nginxのコンフィグファイルをいじります
`/etc/nginx/conf.d/`の中に`てきとうな名前.conf`を作成します
中は
```
server {
 server_name ここがドメイン;

 location / {
  proxy_pass    ここをApacheのURLにする;
 }
}

例:
server {
 server_name reach-world.net;

 location / {
  proxy_pass    http://192.168.11.2:9000/;
 }
}
```
④nginxを再起動します
```
sudo nginx -s reload
```
スマートフォンのWiFiをOFFにして(モバイルデータ通信で)URLでアクセスしてみましょう
アクセスできたら次にすすむ

⑤[このサイト](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04-ja) を見ていい感じに設定しましょう
ステップ2,3,5はやらなくていいです。

⑥接続は以下のようになります
`外<-https->Nginx<-http->Apache`

⑦httpできた通信をhttpsのサイトにリダイレクトできますがDの文字制限
||


## 制作
**sskrc**

---

今後の改良に関する提案やバグ報告は、お気軽にIssueを通してご連絡ください。
