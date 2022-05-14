# Java学び直し

2022-05-14 ~

Java 8 以降は仕事でコーディングしていなかったので改めてJavaプログラマになってみたい。

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
