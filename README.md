MongoDBクエリ解説
=====

「第1回　使ってみようMongoDB」で紹介したように，MongoDBは"RDBライクな検索クエリ"を実行可能です。
今回は，MongoDBで使用するクエリを，慣れ親しんだSQLクエリと比較しながら学んでみましょう。


### RDBとの用語の違い

MongoDBのクエリを解説する前に，まずはRDBとの用語の違いを以下に示します。

RDBでの呼称               |  MongoDBでの呼称
--------------------------|---------
"データベース"            | "データベース"
"テーブル"                | "コレクション"
"行" "レコード"           | "ドキュメント"
"列" "カラム"             | "フィールド"
"インデックス"            | "インデックス"
"プライマリキー"※        |  "プライマリキー"※

※:RDBでは，一意な列（または列の組み合わせ）を明示的にプライマリキーに指定する必要がありますが，
MongoDBでは，一意な"_id"フィールドが自動的に作成されます。

ここで重要なことは，呼称は異なるものの，RDBにおける概念がほぼそのままMongoDBにも適用できるということです。
すなわち，RDBにおいて，データベースが複数のテーブルを持ち，テーブルが複数のレコードを持ち，
レコードがカラムにより区切られているように，MongoDBにおいても，データベースが複数のコレクションを持ち，
コレクションが複数のドキュメントを持ち，ドキュメントがフィールドにより区切られているのです。
これが，MongoDBで"RDBライクな検索クエリ"を実行できる理由です。  
大きな違いは，RDBではテーブル中の全てのレコードが同じカラムを有するのに対し，
MongoDBではドキュメントごとに独自のフィールドを有することができる点です。


### MongoDBにおけるクエリとは

RDBでSQLクエリを使ってデータを取得するように，
MongoDBでは，JavaScriptと，"BSONドキュメント"と呼ばれるJSONに似たクエリ記述オブジェクトを使ってデータを取得します。 
例えば，データベースのusersコレクションに入っているすべてのドキュメントを取得したいとき，
以下のようなクエリを発行します。

    db.users.find({})

このクエリでは，現在のデータベースオブジェクトdbに含まれるusersコレクションオブジェクトのfindメソッドを，
引数なしで呼び出しています。  
次に，last_nameフィールドが"Smith"と一致するドキュメントを取得してみます。
findメソッドの引数に，{[フィールド名]: [検索文字列]}を追加します。これがBSONドキュメントです。
すると，SQLにおけるWHERE句のように，検索結果を絞り込むことができます。

    db.users.find({'last_name': 'Smith'})

さらに，last_nameフィールドが"Smith"と一致するドキュメントの，ssnフィールドだけを取得してみましょう。
findメソッドの引数に，{[フィールド名]: 1}を追加します。
すると，SQLにおけるSELECT句のように，取得するフィールドを指定することができます。

    db.users.find({last_name: 'Smith'}, {'ssn': 1});

逆に，すべてのドキュメントからthumbnailフィールドを除いた結果を取得するには，以下のようにします。

    db.users.find({}, {thumbnail:0});

{[フィールド名]: 0}とすることで，取得しないフィールドを指定できました。  
このように，MongoDBではJavaScriptでメソッドを呼び出し，BSONドキュメントをその引数に指定することで，
SQLのように様々な処理を行うことができます。  
余談ですが，メソッドに"()"をつけずにクエリを実行した場合，メソッドの内部実装を見ることができます。
これは，MongoシェルがJavaScriptでできているためです。


### MongoDBを触ってみよう







