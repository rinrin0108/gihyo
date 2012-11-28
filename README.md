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
これが，MongoDBで"RDBライクなクエリ"を実行できる理由です。  
大きな違いは，RDBではテーブル中の全てのレコードが同じカラムを有するのに対し，
MongoDBではドキュメントごとに独自のフィールドを有することができる点です。


## MySQLと比較したクエリ

"第1回　使ってみようMongoDB"で解説したように，
MongoDBでは，JavaScriptとJSON形式のハッシュデータを使ってデータを操作します。  
ここでは，MongoDBで使用するクエリを，慣れ親しんだMySQLのクエリと比較しながら学んでみましょう。  

### データベース操作
データベースを参照する
* MySQL
<pre>
> show databases
</pre>
* MongoDB
<pre>
> show dbs
</pre>

データベースを選択する
* MySQL
<pre>
> use [データベース名]
</pre>
* MongoDB
<pre>
> use [データベース名]
</pre>

データベースを作成する
* MySQL
<pre>
create database [データベース名]
</pre>
* MongoDB
<pre>
なし
</pre>
MongoDBのデータベースは，データベースを選択し，コレクションへ最初のドキュメントを
insertしたタイミングで作成されます。

データベースを削除する
* MySQL
<pre>
> drop database [データベース名]
</pre>
* MongoDB
<pre>
> db.dropDatabase()
</pre>
MongoDBでは，useコマンドでデータベースを選択しておく必要があります。

### コレクション操作
MongoDBのコレクションは，MySQLのテーブルに該当します。

コレクションを作成する
* MySQL
<pre>
> create table [テーブル名]([スキーマ定義]);
</pre>
* MongoDB
<pre>
不要
</pre>
MongoDBのコレクションは，コレクションへ最初のドキュメントをinsertしたタイミングで作成されます。  
明示的にコレクションを作成したい場合，以下のようにすることもできます。
もちろん，MySQLと違ってスキーマ定義は行いません。
* MongoDB
<pre>
db.createCollection("[コレクション名]")
</pre>

コレクションを参照する
* MySQL
<pre>
> show tables;
</pre>
* MongoDB
<pre>
> show collections
</pre>

コレクションを削除する
* MySQL
<pre>
> drop table [テーブル名];
</pre>
* MongoDB
<pre>
> db.[コレクション名].drop()
</pre>

コレクション内のデータを全て削除する
* MySQL
<pre>
> truncate table [テーブル名];
</pre>
* MongoDB
<pre>
> db.[コレクション名].remove()
</pre>


### ドキュメント操作
MongoDBのドキュメントは，MySQLのレコードに該当します。

#### INSERT
* MySQL
<pre>
> insert into [テーブル名] values([レコードの内容]);
</pre>
* MongoDB
<pre>
> db.[コレクション名].insert([ドキュメントの内容])
</pre>
MongoDBでは，JavaScriptをクエリに使用します。
したがって，以下のようにfor文を使ってドキュメントをinsertすることも可能です。
* MongoDB - JavaScriptを使用した例
<pre>
> for(var i=1; i<=20; i++) db.testcoll.insert({"stock":i})
</pre>

#### SELECT
まずは，コレクションの中の，全ドキュメントの全フィールドを取得してみます。
MySQLでいうと，テーブルの中の，全レコードの全カラムを取得することと同じです。
* MySQL
<pre>
> select * from [テーブル名];
</pre>
* MongoDB
<pre>
> db.[コレクション名].find()
</pre>
MongoDBでは，クエリを実行した結果，標準で20件以上のドキュメントが返された場合，
19件目を表示した次の行に"has more"と表示されます。
続きのドキュメントを表示したい場合は，以下のようにします。
* MongoDB
<pre>
> it
</pre>
itは"イテレータ"を意味するコマンドで，次の19件を表示することができます。  
一度に全てのドキュメントを表示したい場合は，以下のようにします。
* MongoDB
<pre>
> db.marunouchi.find().toArray()
または
> db.marunouchi.find().toArray().forEach(printjsononeline) 
</pre>
なお，一度に表示できるドキュメントの数を変更したい場合は，次のようにします。
* MongoDB
<pre>
> DBQuery.shellBatchSize = [一度に表示したいドキュメント数]
</pre>

件数を指定して取得するには，以下のようにします。  
まずは，1件だけ表示したい場合です。
* MySQL
<pre>
> select * from [テーブル名] limit 1;
</pre>
* MongoDB
<pre>
> db.[コレクション名].findOne()
</pre>
次に，n件表示したい場合です。
* MySQL
<pre>
> select * from [テーブル名] limit [n];
</pre>
* MongoDB
<pre>
> db.[コレクション名].limit([n])
</pre>

次に，コレクションの中のドキュメント数をカウントしてみます。
* MySQL
<pre>
> select count(*) from [テーブル名];
</pre>
* MongoDB
<pre>
> db.[コレクション名].count()
</pre>

今度は，取得するフィールドを指定してみましょう。
コレクションの中の，全ドキュメントの特定のフィールドだけを表示してみます。
* MySQL
<pre>
> select [カラム名1],[カラム名2],...,[カラム名n] from [テーブル名];
</pre>
* MongoDB
<pre>
> db.[コレクション名].find({},{"[フィールド名1]":1,"[フィールド名2]":1,...,"[フィールド名n]":1})
</pre>
この場合，_idフィールドは常に表示されます。
_idフィールドを含め，特定のフィールドを非表示にしたい場合は，以下のようにします。
* MongoDB
<pre>
> db.[コレクション名].find({},{"[フィールド名1]":0,"[フィールド名2]":0,...,"[フィールド名n]":0})
</pre>
これらの表示／非表示は，クエリに混在させることができます。
* MongoDB - フィールドの表示／非表示が混在した例
<pre>
> db.testcoll.find({},{"_id":0,"key1":1,"key2":0})
</pre>

ここまで書いた
--------

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



descコマンドはありません // mysql> desc {table_name}


##### ちょっと脱線 
* ハッシュであるdbのキー一覧を表示してみる
<pre>
> for(var k in db) print(k)
> //versionというキーあり、呼んでみる
> db.version
> db.version()
</pre>
