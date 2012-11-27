MongoDBクエリ解説
=====

「第1回　使ってみようMongoDB」で紹介したように，MongoDBは”RDBライクな検索クエリ”を実行可能です。
今回は，MongoDBで使用するクエリを，慣れ親しんだSQLクエリと比較してみましょう。

### MongoDBにおけるクエリとは

RDBでSQLクエリを使ってデータを取得するように，
MongoDBでは，”BSONドキュメント”と呼ばれるJSONに似たクエリ記述オブジェクトを使ってデータを取得します。  
例えば，usersコレクションに入っているすべてのドキュメントを取得したいとき，以下のようなクエリを発行します。

    db.users.find({})

次に，"last_name"が"Smith"であるドキュメントを取得してみます。

    db.users.find({'last_name': 'Smith'})

このように，
