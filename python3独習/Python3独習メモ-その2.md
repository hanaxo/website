# Python3独習メモ その2(2022-01-12)

[前回](./Python3独習メモ-その1.md) の続き

## 同値性と同一性

同値性は==演算子、同一性はis。

'abc'と同じ値の文字列を別々に生成してもpython実行環境がオブジェクトを再利用し同一性を持つ場合もあるみたいなので、どちらの演算子でもOKではなく、文脈に沿った正しい演算子を使うのが良い。

Noneの評価はisを使うお作法。pythonには演算子をオーバーロードする仕組みがあるため==演算子ではNoneと言い切れないためisを使うらしい。
```python
hoge == None # オーバーロードされて None 以外でも真って実装もありうるので
hoge is None # 普通はこっち。
```

* ということはisは演算子じゃない何か？
* NoneType型はシングルトンでインスタンスはユニーク？

## 論理演算子

* && || ! ではなく and or not
* 排他的論理和だけ記号で ^
* false and true, true or false のとき右辺は評価されない(短絡演算)
* x > 0 and x < 10 は 0 < x < 10 と書けて便利

## if

* 前回学んだようにブロックをインデントで表現することに注意<br>
  しくじると IndentaitionalError
* 空ブロックも同様にエラーになるため pass を使う
```python
if 条件式:
print('hoge') # IndentationalError
elif 条件式: # これも IndentationalError
elif 条件式:
    pass # nop
```

## switch/match

* switchはifで実装できるよねと[公式見解](https://docs.python.org/ja/3/faq/design.html#why-isn-t-there-a-switch-or-case-statement-in-python)
* matchがver3.10で追加された<br>
    * https://docs.python.org/ja/3/tutorial/controlflow.html#match-statements
    * https://www.python.jp/news/wnpython310/index.html
    * ほぼswitchと同じ書き方
    * 一致する最初のパターンのみ実行されるので break はない
    * default: は case _:
