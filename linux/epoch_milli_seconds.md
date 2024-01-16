エポックミリ秒という単位
---

datadog の metrics がめちゃめちゃでかい epoch time を返していたので調べた
ちゃんと書いてたけど最初 46 万年とかなって dd ぶっ壊れてるかと思った
https://docs.datadoghq.com/logs/processing/parsing/?tab=matcher#parsing-dates

4.6 万年とか表示されていたが
```code
❯ gdate -d @1412978400000
Tue Jul  3 16:00:00 GMT 46745
```

1000 するか、下 4 桁目で `.` 打ってあげるといい感じになる

```code
❯ gdate -d @1412978400.000
Fri Oct 10 22:00:00 GMT 2014
```


https://qiita.com/hirokapi/items/fb4b64b65b63d4ff0782
