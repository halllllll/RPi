## ラズパイゼロWでカメラをやりたい
とりあえず適当に調べた方法でやってみる。いくつかソフトや専用のディストリビューションOSがあるらしいがよくわからん

## RPiでカメラをするときのソフトなど
調べるといくつものソフトやRPi用の監視カメラ特化型ディストリビューションがあったりしたので軽くまとめる（あくまで5ググりくらいの調査結果であり網羅でも詳細でもないし間違っていたらごめんなさい）
### Pi Cmaera(設定)
要はRPiでカメラを使えるようにする設定（たぶん）。[ここ](http://tatata.hatenablog.jp/entry/2017/03/26/220111)などを参考にして設定からオンにしてやるとかそういうやつ。

### FSWEBCAM
USBカメラからスチルを撮るパッケージっぽいやつ。
[ここ](http://igarashi-systems.com/sample/translation/raspberry-pi/usage/webcam.html)

### Motion
motionとはLinux向けのCベースなGPL2.0下のオープンソースCUIソフトで、カメラはhttp経由でコントロールできるらしい。jpg撮影のほか、動画撮影、動体検知もいける。[公式はこちら](http://htmlpreview.github.io/?https://github.com/Motion-Project/motion/blob/master/motion_guide.html)

### MotionPie
RPiむけカメラOSのディストリビューション。イメージをmicroSDに焼いて起動すれば使える。設定などはPCからブラウザを通じてやる。動体検知、録画の保存期間選択などができる。後述するmotioneyeとは開発者が同じらしい

### motioneye
Linux向けのカメラソフト。Pythonで書かれているらしい。これもWebベースでの操作。動体検知に加えタイムラプスや外部クラウドへのアップロードが可能。多くのUSBカメラとの互換性があるらしい。[qiita記事](http://qiita.com/mt08/items/96207abfba91954b50d4)</br>
ふつうのLinuxだとこれを入れればいいが、RPi向けには後述するmotionEyeOSを使う。

### motionEyeOS
フロントエンドにmotion, バックエンドにmotioneyeを配したディストリビューション。2017年4月時点でRPi Zero Wに対応している。[qiita記事](http://qiita.com/mt08/items/180140d7df549b0a1dec)

### RPi-Cam-Web-Interface
[こちら](http://www.e-risingstar.com/?p=1324)に詳しい。あと[からあげさんも使っていた。](http://karaage.hatenadiary.jp/entry/2017/04/06/073000)

<p>とりあえず設定とコマンド叩くだけでいけるPi Cmaeraと、専用ソフトのRPi-Cam-Web-Interface、そして専用OSのmotionEyeOSをやってみる</p>

## Pi Camera
[これ](http://www.neko.ne.jp/~freewing/raspberry_pi/raspberry_pi_zero_camera_setup/)はRaspbianでやってみるらしいのでとりあえずなぞる。フレキケーブルの接続まで写真で載っていて非常に親切。

### セットアップするまでのハマりどころ
- ふつうにNOOBSから最新のやつを解凍して入れたのだが、4GB以上でないとダメと言われた（4GBのmicroSDにいれており、Raspbianのインストールで弾かれた）ので黙ってちゃんと用意して入れた
- マウスとキーボード用にUSBハブをつなげてインストールを待ったのだが、どちらか一方しか使えず、ハブごと抜いてデヴァイスを切り替えようとするとウィンドウが暗くなってからセットアップ画面に移行した。ハブを介さないデバイスの切り替えだとちゃんとホットプラグで動作する。</br>
完全にセットアップが終わってGUI画面（デスクトップ）が立ち上がってからハブをつなぎ、そこからマウスとキーボードを挿すと両方共ちゃんと起動した。</br>
USBの電源供給のせいかもしれない。未確認

### vcgencmd
raspberry pi内部の情報を取得するコマンド集。CPU温度や周波数などもいける。[一覧](http://elinux.org/RPI_vcgencmd_usage)</br>
たとえば<code>vcgencmd measure_temp</code>でCPU温度の取得など。
<code>vcgencmd get_camera</code>でカメラが使えるかどうか確認する。

### raspistill
そのまんま、RPiで静画を撮るやつ。デフォルトでは<code>/home/pi</code>以下にそのまま保存される。
<code>
mkdir /home/pi/image
raspistill -w 1280 -h 720 -o /home/pi/image/test.jpg
</code>
などとしてやったり、時間指定での連続撮影や簡単なエフェクトなんかもできるらしい。

### raspivid
動画をとるやつ。

### 感想
一番手っ取り早い。これが基本になってほかのソフトとかに組み込まれているんじゃないの（わからん）

## RPi-Cam-Web-Interface
上のほうで挙げたリンクを参考に。
### ハマりどころ
- 現行バージョン（6.3.14）では<code>chmod u+x *.sh</code>とかいう謎コマンドは必要なく、</br>
<code>
git clone https://github.com/silvanmelchior/RPi_Cam_Web_Interface.git</br>
cd RPi_Cam_Web_Interface</br>
./install.sh
</code></br>でいける。
[参考](https://dantheiotman.com/2017/08/15/video-surveillance-with-a-pi-zero-w-zeroview-and-rpi-cam/)

- 保存先をUSBメモリにしたかったのだがどこをどういじればいいのかわからない（未解決）。
おそらく[こいつ](http://elinux.org/RPi-Cam-Web-Interface#How_do_I_change_the_path_for_the_video_images_and_pictures.3F)とかを参考にすれば勘の良い人なら少しは分かるんだろうけどわからん。
<code>RPi_Cam_Web_Interface/install.sh</code>したときにConfigで<strong>Cam Subfolder</strong>のルートが<code>/var/www</code>になっているっぽく、これを無理矢理かえたらいけるのかもしれんが。
- 最初 <code>apt-get</code>とか無視してたらApacheが無いとかかんとかいわれたのでおとなしく<code>update</code>と<code>upgrade</code>をしてからやった。

### 感想
すごい。動体検知もちゃんと動作するようだ。専用の動画プレイヤーまでついている。
やっぱRPiWzeroだと遅い。
ついでによくある自動起動もやってみた。
<code>/etc/rc.local</code>の<code>exit 0</code>以前に書いたコードが起動時に走るらしい。
<code>RPi_Cam_Web_Interface/start.sh</code>とし、さらに、WebUIの設定を<string>Edit schedule settings</string>の最下段の<string>Period Start</string>に<code>md 1</code>としておくことで、起動時から動体検知をオンにしてみた。


## motionEyeOSを始める
[todo:]
