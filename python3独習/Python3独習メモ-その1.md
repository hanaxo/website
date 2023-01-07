# Python3独習メモ その1(2023-01-08)

コンピュータを触り始めた2000年頃、PythonはLinuxユーザに人気の言語だったと思います。私個人はインタプリタは遅いらしいからPythonは必要ないだろうと感じていました。

その少し後に lightweight language(LL) が流行ったものの rails 一色。Pythonと言えばメジャーバージョンアップで互換性なくなり、古いバージョン使う人が多い言語というのが当時の印象。

2010年台に徐々に状況が変わり、機械学習、言語別年収ランキング、様々な文脈でPythonが目立ってきました。私個人としては新卒の若者たちにpython使いが増えていると実感しています。

同じチームの若い人と共通の体験をする意味でもPythonを勉強しようと思いました。
書籍「独習Python」を中心に学び、気になった箇所をまとめます。

## Pythonとは

* グイド・ヴァン・ロッサムさんが開発、1991年にリリース
* 美しい、明示、シンプルなどの思想(import thisを参照)
* インタプリタ
* マルチパラダイム(オブジェクト指向 + 関数型)
* Battery Included(標準ライブラリが豊富、プロファイルらも標準で)
* サードパーティーライブラリはさらに豊富


## 言語仕様としてのインデント

同じ数のスペース or タブでインデントされた文がブロックになります。不正な場合は IndentationError が発生。

```python
if True:
    print('hoge')

実行結果
hoge
```

```python
if True:
print('hoge')

実行結果
  File "<stdin>", line 2
    print("a")
    ^
IndentationError: expected an indented block after 'if' statement on line 1
```
## コメント

```python
# 言語仕様上は # のみで、/* 複数行コメントはない */

"""トリプルクォーテーションを用いた
文字列リテラルをもって複数行コメントすることもある。
インタプリタ的にはただの文字列だからインデント間違えると IndentationError が発生"""
```

トリプルクォーテーションについては [str](#string) を参照

## 型

### bool

組み込み定数 True と False

if文など論理値を必要な状況では None, 0, 空文字は False となる。

### 数値型

```python
# int
dec = 256
bin = 0b100000000
oct = 0o400
hex = 0x100
value = 1_234_567 # 数値セパレータで桁数多くてもわかりやすく書ける

# float
pi1 = 3.14159265359
pi2 = 3.141_592_653 # 数値セパレータ
exponent1 = 1.23e10 # eは大文字でも良い
exponent2 = 1.23e-10

# complex(複素数)型
c = 2 + 3j # 実部 + 虚部j。iではなくj or J
print('実部=', c.real, ' 虚部=', c.imag)
i = 3j # 虚数。
```

数値セパレータは言語仕様上、3桁区切りである必要はないそうです。

<a id="string"></a>
## 文字列

テキストシーケンス型。文字列操作は [本家strのメソッド説明](https://docs.python.org/ja/3/library/stdtypes.html#string-methods) に詳しい。

```python
print('シングルクォーテーションでも')
print("ダブルクォーテーションでもOK")
print('文字列リテラルに "ダブルクォーテーション" をエスケープせずに入れられる')
print("文字列リテラルに 'シングルクォーテーション' をエスケープせずに入れられる")
print(' \' エスケープシーケンス \" もあるよ')

# 文字列の連結
a = 'hoge' 'fuga'
print(a)
print('foo' 'bar')
a = (
    'fizz'
    'buzz'
)
print(a)

# 複数行
print('''改行を含む文字列は
トリプルクォーテーション(三重引用符)''')
print("""ダブルクォーテーション
3つでもOK""")

# エスケープシーケンス無用のRAW文字列
print(r'c:\windows\path\') # 先頭に r or R

# フォーマット済み文字列リテラル(formatted string literal or f-string)
# ver3.6で追加
socre = 92
print(f'あなたのスコアは{score}点です！') # 先頭に f or F

# raw + f-string
username = 'foo'
print(fr'{username} のホームディレクトリは c:\users\{username}')

# 文字コード
print('unicode 6674 は "\u6674"')
```

### リスト型

配列的なもの。シーケンス型の中のリスト型というらしい。リスト型はミュータブル、イミュータブルなものとしてtuple型がある。

リストの操作については [ここ](https://docs.python.org/ja/3/library/stdtypes.html#common-sequence-operations) と [ここ](https://docs.python.org/ja/3/library/stdtypes.html#mutable-sequence-types) が一番わかりやすかったです。

```python
fruits = ['バナナ', 'りんご', 'みかん', 'いちご']
print(fruits[3]) # いちご、と出力される
print(fruits[4]) # IndexError 発生
fruits[0] = 'ばなな' # リスト型はミュータブル

# 複数行の記載もできる
seasons = [
    '春',
    '夏',
    '秋',
    '冬', # 最後尾要素のあとにもカンマOK
]

# 入れ子にして二次元配列的な、Excel座標を例に
coordinates = [
    ['A1', 'B1', 'C1'],
    ['A2', 'B2', 'C2'],
    ['A3', 'B3', 'C3', 'D3'],
]
print(coordinates[1][2]) # 出力は C2
```

### 定数

const, final修飾子のような定数の仕組みはない。
TAX_RATE = 1.10 のように大文字の変数で定義することがコード標準(PEP8)で推奨されている。

## 演算子

```python
# 除算で切り捨てができる
print(10 // 3) # 出力は 3

# インクリメント・デクリメント演算子がないので
i += 1
i -= 1

# 代入演算子(:=)
# 代入は式ではないため代入演算子がver3.8で追加された
y = (x := 4) / 2
print(f'x={x}, y={y}') # 出力は x=4, y=2.0
```

Javaの代入演算子的な状況
```java
// こういうのは見たことないが
int x;
int y = (x = 4) / 2;

// Java8より前のテキストファイルの読み込みではよく見かけた
// メソッドの戻り値を代入しつつ評価する。
// pythonでは変数の宣言行が不要になるのでコードがより短くなるのが利点か？
String line;
while ( (line = bufferedReader.readLine()) != null ) {
    ...
}
```

## 浮動小数点の演算

2進数の循環小数を含む演算は Decimal 型 を用いる。Java の BigDecimal みたいな、でも加減乗除の演算子が利用できる優れもの。
