## RPI0Zでwpa_suppicant.confでうまくつながってくれない

ふつうに<code>sudo nano /etc/wpa_supplicant/wpa_supplicant.conf</code>で、</br>
<code>
  ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
  update_config=1
  country=GB
  network={
          ssid=任意のSSID
          psk=SSIDのパスワード
  }
</code>
としてもなんかしらんけど反映されず（起動時につながらない）…

既につなげて運用しているRPi3wの設定をパクることに。

## やり方
パクリ元で
<code>
cat /etc/wpa_supplicant/wpa_supplicant.conf
</code>
として表示されたやつを見てみると、</br>
<code>
ctrl_interface=/var/run/wpa_supplicant</br>
network={</br>
    scan_ssid=1</br>
    ssid="つなげたいSSID"</br>
    psk="つなげたいSSIDのパスワード（平文）"</br>
    priority=1</br>
}</br>
network={</br>
    scan_ssid=1</br>
    ssid="つなげたいSSID"</br>
    psk="つなげたいSSIDのパスワード（平文）"</br>
    wep_tx_keyidx=0</br>
    priority=1</br>
}
</code></br>
となっていた。このように<code>priority</code>ってオプションのフラグを立てておけばいいのではないか？

### 結果
いけました(๑•̀ㅂ•́)و✧

## 経過その他
- [todo:]
