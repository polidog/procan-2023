# データベースの使い方

今回は書籍管理するために、データベースを利用します。  
現代のWebアプリケーションは、情報(データ)保持するために必ずと言っていいほどリレーショナルデータベースを使います。  

今回はデータベース製品としてMySQLを使います。  

# そもそもデータベースとはなにか？

https://blog.codecamp.jp/database-basic-beginner  


# SQLについて

データベースを操作するために、SQLという言語を利用します。基本的には SELECT, INSERT, UPDATE, DELETEの４つの構文を使います。  

- SELECT -> データの取得
- INSERT -> データの新規登録
- UPDATE -> データの更新
- DELETE -> データの削除

という意味合いになっています。

たとえばデータを取得する場合はこのように操作します。

```sql
SELECT * FROM items
```

データを登録するときは以下のようにします。

```sql
INSERT INTO items (item_code, name, image) VALUES ('shop:100303003', 'ソファ2P', 'https://thumbnail.image.rakuten.co.jp/@0_mall/f142123-atsugi/cabinet/image2/537z.jpg?_ex=128x128')
```


## 実際にデータベースを操作してみよう

実際にデータベースを触ってみましょう。 ここでは以下の作業をします。  

- テーブルの作成
- データをテーブルに登録する
- 登録したデータを表示する

## テーブルの作成


まずはDBに接続します。


```shell
$ docker compose exec db /bin/bash
bash-4.4# mysql -u root -proot wishlist
```

まずはテーブルがあるか確かめましょう。
確かめるためには `show table` コマンドを実行します。

```shell
mysql> show tables;
Empty set (0.00 sec)
```

この時点ではテーブルがありません。
まずはテーブルを作成しましょう。

```sql
create table items (
  id INT AUTO_INCREMENT NOT NULL, 
  name VARCHAR(255) NOT NULL, 
  item_code VARCHAR(255) NOT NULL, 
  image VARCHAR(255), 
  like_count SMALLINT UNSIGNED NOT NULL DEFAULT 0,
  created_at DATETIME NOT NULL, 
  updated_at DATETIME NOT NULL,
  PRIMARY KEY(id)
) DEFAULT CHARACTER SET utf8mb4 COLLATE `utf8mb4_unicode_ci` ENGINE = InnoDB;
```

もう一度 `show tables` をすると itemsテーブルが作成さています。

```shell
mysql> show tables;
+--------------------+
| Tables_in_wishlist |
+--------------------+
| items              |
+--------------------+
1 row in set (0.02 sec)
```

### テーブルの削除

テーブルを消したい場合はdrop文を使います。

```
drop table テーブル名
```

で消すことができます。


## データをテーブルに登録する

テーブルを作成したら、次にデータを入れてみましょう。

```sql
INSERT INTO items (item_code, name, image, created_at, updated_at) VALUES ('test:1112222', "テスト商品", 'https://thumbnail.image.rakuten.co.jp/@0_mall/air-rhizome/cabinet/117/l2l-kago1-ar.jpg', now(), now());
```

## 登録したデータを表示する

データを登録したら、次はデータを確認してみましょう。


```sql
SELECT * FROM items;
```

これでitemsに登録されているすべてのデータを確認できます。  
また条件を絞ることもできます。  
たとえばISBNで絞る場合は以下のようにします。  

```sql
SELECT * FROM items WHERE item_code = 'test:1112222';
```

## 接続の終了について

mysqlへの接続を終了するには `exit` コマンドを使います。

```shell
mysql> exit
```

最後にターミナル接続も終了させましょう。

```shell
bash-4.4# exit
```