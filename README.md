MongoDBクエリ解説
=====

「第1回　使ってみようMongoDB」で紹介したように，MongoDBは"RDBライクなクエリ"を実行可能です。
今回は，RDBと比較しながら，MongoDBのクエリについて学びたいと思います。

## RDBとの用語の違い

MongoDBのクエリを解説する前に，まずはRDBとの用語の違いを明確にしておきます。

RDBでの呼称               |  MongoDBでの呼称
--------------------------|---------
"データベース"            | "データベース"
"テーブル"                | "コレクション"
"行" "レコード"           | "ドキュメント"
"列" "カラム"             | "フィールド"
"インデックス"            | "インデックス"
"プライマリキー"          |  "プライマリキー"

ここで重要なことは，呼称は異なるものの，RDBにおける概念がほぼそのままMongoDBにも適用できるということです。
すなわち，RDBにおいて，データベースが複数のテーブルを持ち，テーブルが複数のレコードを持ち，
レコードがカラムにより区切られているように，MongoDBにおいても，データベースが複数のコレクションを持ち，
コレクションが複数のドキュメントを持ち，ドキュメントがフィールドにより区切られているのです。
これが，MongoDBで"RDBライクな検索クエリ"を実行できる理由です。  
大きな違いは，RDBではテーブル中の全てのレコードが同じカラムを有するのに対し，
MongoDBではドキュメントごとに独自のフィールドを有することができる点です。


## MySQLと比較したクエリ

"第1回　使ってみようMongoDB"で解説したように，
MongoDBでは，JavaScriptとJSON形式のハッシュデータを使ってデータを操作します。  
ここでは，MongoDBで使用するクエリを，慣れ親しんだMySQLのクエリと比較しながら学んでみましょう。  

### データベース操作
* データベースを参照する // mysql> show databases
<pre>
> show dbs
</pre>

* データベースを選択/作成する // mysql> use {db_name}; create database {db_name}  
MongoDBのデータベースは、選択してコレクションへ最初のドキュメントをinsertしたタイミングで作成されます。
<pre>
> use {db_name}
</pre>

* データベースを削除する // mysql> drop database {db_name}
<pre>
//useコマンドでデータベースを選択しておく    
> db.dropDatabase()
</pre>

### コレクション操作
* コレクションを参照/作成する // mysql> show tables; create table {table_name}(...)
<pre>
> show dbs  
> use {db_name}  
> show collections  //コレクションが何も表示されなかったら適当にinsertする  
> db.marunouchi.insert({"created_at":new Date()})  //現在時刻をinsert  
> show collections //marunouchiが見えますか
</pre>

* コレクションを削除する // mysql> drop table {table_name}
<pre>
> show dbs  
> use {db_name}  
> show collections  
> db.marunouchi.drop()  //コレクション全部を削除します  
> show collections //確認、marunouchiは削除された  
</pre>

* コレクション内のデータを削除する // mysql> truncate table {table_name}
<pre>
> db.marunouchi.insert({"created_at":new Date()})  
> show collections  
> db.marunouchi.remove() //コレクションの中のすべてのオブジェクトを削除します  
> show collections //確認、marunouchiはまだある  
</pre>

* descコマンドはありません // mysql> desc {table_name}

### ドキュメント操作
#### INSERT
* mysql> insert into {table_name} values(...)
<pre>
> use {db_name}
> db.marunouchi.insert({"created_at":new Date()})
> db["marunouchi"].insert({"created_at":new Date()}) //こんな書き方もできます 
> for(var i=1; i<=20; i++) db.marunouchi.insert({"stock":i}) //for文も使えます
</pre>


##### ちょっと脱線 
* ハッシュであるdbのキー一覧を表示してみる
<pre>
> for(var k in db) print(k)
> //versionというキーあり、呼んでみる
> db.version
> db.version()
</pre>


#### SELECT
* mysql> select count(*) from marunouchi
<pre>
> db.marunouchi.count()
</pre>

* mysql> select * from marunouchi
<pre>
> db.marunouchi.find()
</pre>

* has more と表示されたら
<pre>
> it //iterator
</pre>

* find()で20件以上表示させたい
<pre>
> DBQuery.shellBatchSize = 300  
もしくは  
> db.marunouchi.find().toArray()  
> db.marunouchi.find().toArray().forEach(printjsononeline)  
</pre>


* とりあえず1件表示 // mysql> select * from marunouchi limit 1
<pre>
> db.marunouchi.findOne()
</pre>

* mysql> select * from marunouchi limit 5
<pre>
> db.marunouchi.find().limit(5)
</pre>

* mysql> select _id from marunouchi
<pre>
> db.marunouchi.find({},{"_id":1})  
> db.marunouchi.find({},{"created_at":1}) //_id フィールドは常に表示される  
> db.marunouchi.find({},{"_id":0,"created_at":1}) //0で非表示に  
</pre>

* mysql> select _id from where stock = 10
<pre>
> db.marunouchi.find({"stock":10}, {"_id":1})  
</pre>

* mysql> select _id from where stock {>, <, >=, <=} 10
<pre>
> db.marunouchi.find({ "stock": { $gt:  10 } }, { "_id": 1 })
> db.marunouchi.find({ "stock": { $lt:  10 } }, { "_id": 1 })
> db.marunouchi.find({ "stock": { $gte: 10 } }, { "_id": 1 })
> db.marunouchi.find({ "stock": { $lte: 10 } }, { "_id": 1 })
</pre>

* JSON形式で表示
<pre>
> db.marunouchi.find().forEach(printjson)  
> db.marunouchi.find().forEach(printjsononeline)  
</pre>

* toArray
<pre>
> db.marunouchi.find().toArray()
</pre>

#### UPDATE
* mysql> update marunouchi set version = 7 where name = 'debian'
<pre>
> db.marunouchi.update({"name":"debian"},{$set:{"version":7}}) //$setがないと他のフィールドが消えてしまうので注意
</pre>

* _idが存在すればupdate、存在しなければinsert
<pre>
> db.marunouchi.save({"_id":ObjectId("xxxx"),"version":7})
</pre>

#### DELETE
* mysql> delete from marunouchi where name = 'centos'
<pre>
> db.marunouchi.remove({"name":"centos"})
</pre>

### インデックス操作
* INDEX参照
<pre>
> db.system.indexes.find()
</pre>

* INDEX作成
<pre>
> db.marunouchi.ensureIndex({"stock":1})
</pre>

* INDEX削除
<pre>
> db.marunouchi.dropIndex({"stock":1})  
> db.marunouchi.dropIndexes() //全て削除  
</pre>





