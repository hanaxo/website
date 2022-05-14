# Java学び直し

2022-05-14 ~

Java 8 以降は仕事でコーディングしていなかったので改めてJavaプログラマになってみたい。

## Java 7

### 複数の例外をまとめてキャッチできるようになった
```java
try {
	...
} catch ( NullPointerException | NumberFormatExcetpion e ) {
	logger.error(e);
}
```

### ダイヤモンド演算子

Java 5 で導入されたジェネリクス(総称型)の書き方がちょびっと省略できるようになった。
```java
List<String> list = new ArrayList<String>();
List<String> list = new ArrayList<>(); // ちょびっと省略
```

### try-with-resources 文

開けたら閉める的なお作法からの解放。勝手に閉じてくれる。finally でさらにキャッチとか嫌な思い出。  
Closeable の上にできた AutoCloseable インターフェース限定。
```java
-- try でリソース指定。finally に reader#close(); 書かなくても勝手に閉じてくれる。
try (FileReader reader = new FileReader(new File("hoge.txt"))) { 
	...
} catch (Exception e) {
    logger.error(e);
} 
```

### switch文で文字列を評価できるようになった

従来は int, enum だけ？

## Java 8

一日かけてラムダ式とStream APIを勉強したが、書いたコードとまとめ資料が消えてしまった。
メモ程度になるが改めて。

### ラムダ式
関数型がJavaにもやってきた。

```java
public static void main(String[] args) {

	// レモンサワー飲みたい

	// 従来の書き方
	String lemonSour = makeSour("レモン");
	drink(lemonSour);

	// 関数型の書き方イメージ。コンパイル通らないよ
	// 引数に関数を渡す
	drink(makeSour("レモン"));

	// Java 8 で用意された Function インターフェースを使った書き方　
	// インターフェースなので匿名クラス
	Function<String, String> sourMkaer1 = new Function<String, String>() {
		public String apply(String fruit) {
			return fruit + "サワー";
		}
	};
	drink(sourMkaer1, "レモン");

	// もっとコンパクトに書くことができる。これがラムダ式！
	Function<String, String> sourMkaer2 = (fruit) -> { return fruit + "サワー"; };
	drink(sourMkaer2, "レモン");
}

private static String makeSour(String fruit) {
	return fruit + "サワー";
}

private static void drink(String sour) {
	System.out.println(sour + "うまい！");
}

private static void drink(Function<String, String> sourMaker, String fruit) {
    String sour = sourMaker.apply(fruit);
	System.out.println(sour + "うまい！");
}
```

Function インターフェースは引数一つ受け取って結果を返す。引数二つ必要なら BiFunction がある。他にも戻り値のない Consumer, boolean を返す Predicate などなど。
これらをひっくるめて「関数型インターフェース」と呼ぶ。

#### 関数型インターフェース

- ソースコード的には @FunctionalInterface アノテーションがついたインターフェース
- メソッドは必ず一つだけ
	- `Function<String, String> sourMkaer2 = (fruit) -> { return fruit + "サワー"; };`  
      ラムダ式で #apply メソッドをオーバーライドし、匿名ラクスを作っていると言える  
      メソッド複数だとどれをオーバーライドするのか判断つかない
- 一覧  
  https://docs.oracle.com/javase/jp/8/docs/api/java/util/function/package-summary.html


#### その他

いくつかのサイトで学んだが、ラムダ、ラムダ計算、ラムダ式の3つがあった
- ラムダ
	- ラムダ式を指したり、関数型プログラミングを指したりとまちまち
- ラムダ計算
	- 関数型プログラミングの基盤となる理論
- ラムダ式
	- 式(表現)なので書き方くらいの認識で良い気がする
	  https://www.oracle.com/jp/java/technologies/javase/8-whats-new.html
	  > ラムダ式を使用すると、機能をメソッドの引数として処理（データとしてコーディング）することができます。ラムダ式により、単一メソッドのインタフェース（関数型インタフェースと呼ばれる）のインスタンスをより簡潔に表現できます。

とりあえず「ラムダ」と省略するのは控える。

### Stream API

for/foreach と if文を使ったループ処理をパイプライン的に記述できる。

```java
public static void main(String[] args) {

	List<String> fruits = Arrays.asList("りんご", "シチリアンレモン", "みかん", "グリーンレモン");

	// レモンください
	List<String> lemons = fruits.stream()
		// レモンを探す。filterの引数はPredicateなのでラムダ式でOK
		.filter(a -> a.contains("レモン"))
		// リストを作る
		.collect(Collectors.toList());

	// 早くレモンサワー飲みたいな
	// Optional の説明は後回し
	Optional<String> sour = fruits.stream()
		// これだけで並列処理になる
		.parallel()
		// レモンを探す。filterの引数はPredicateなのでラムダ式でOK
		.filter(a -> a.contains("レモン"))
		// サワーを作る
		.map(a -> a + "サワー")
		// 並列で処理したどれか一個
		.findAny();
	sour.ifPresent(a -> System.out.println(a + "うまい！"));
}
```

### Optional

### JSR 310: Date and Time API
