# Web APIの使い方

今回はWeb APIを使って商品検索を行います。  
[Web APIとは何なのか](https://qiita.com/NagaokaKenichi/items/df4c8455ab527aeacf02)


# 今回使う商品検索のWeb APIについて

実際に商品検索するのにあたって楽天の商品検索APIを利用します。  
https://webservice.rakuten.co.jp/documentation/ichiba-item-search

まずは、商品のキーワードを入力したら、そのキーワードに関連する商品情報が取得するコードを書いてみましょう。  
publicディレクトリの中に `api1.php` というファイルを作りましょう。  
作り終わったら、以下のコードを書いてください。    　

```php
<?php

// public/api1.php

const APP_ID = 'xxxxxx'; // Application ID
const BASE_API_URL = 'https://app.rakuten.co.jp/services/api/IchibaItem/Search/20170706?applicationId=%s&keyword=%s&formatVersion=2'; // APIのURL

// 検索したい商品名
$keyword = 'ソファ';

// 検索するURLを生成する https://app.rakuten.co.jp/services/api/IchibaItem/Search/20170706?applicationId=xxxxxx&keyword=ソファ&formatVersion=2
$url = sprintf(BASE_API_URL, APP_ID, $keyword);

// APIからデータを取得する
$content = file_get_contents($url);

// JSON形式のデータを連想配列に変換する
$json = json_decode($content, true);

echo '<pre>';
print_r($json);

```

書き終わったら、[http://localhost/api1.php](http://localhost/api1.php)にアクセスしてみましょう。  

# HTMLをつかって取得した情報をブラウザで表示する

print_rでの出力だと画像が表示できないので、HTMLを使って画像などの情報を表示させてみます。  
publicディレクトリの中に `api2.php` というファイルを作りましょう。  
作り終わったら、以下のコードを書いてください。  

```php
<?php
// public/api2.php

const APP_ID = 'xxxxxxx'; // Application ID
const BASE_API_URL = 'https://app.rakuten.co.jp/services/api/IchibaItem/Search/20170706?applicationId=%s&keyword=%s&formatVersion=2'; // APIのURL

// 検索したい商品名
$keyword = 'ソファ';

// 検索するURLを生成する https://app.rakuten.co.jp/services/api/IchibaItem/Search/20170706?applicationId=xxxxxx&keyword=ソファ&formatVersion=2
$url = sprintf(BASE_API_URL, APP_ID, $keyword);

// APIからデータを取得する
$content = file_get_contents($url);

// JSON形式のデータを連想配列に変換する
$json = json_decode($content, true);
?>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>欲しい物リスト - 検索</title>
</head>
<body>
    <h1>欲しい物リスト - 検索</h1>
    <ul>
        <?php foreach ($json['Items'] as $item): ?>
            <li>
                <a href="<?php echo $item['itemUrl']; ?>" target="_blank">
                    <?php if (isset($item['mediumImageUrls'][0])) : ?>
                    <img src="<?php echo $item['mediumImageUrls'][0]; ?>" alt="">
                    <?php endif; ?>
                </a>
                <p><?php echo $item['itemName']; ?></p>
                <p><?php echo $item['itemPrice']; ?>円</p>
            </li>
        <?php endforeach; ?>
    </ul>
</html>

```

上記のコードをブラウザで実行すると以下のように表示されます。  

<p class="alert">
ちなみに最近のPHPはこのようにHTMLファイルの中にPHPのコードの埋め込むようなことはセキュリティ的な観点からあまり推奨されません。  <br>
テンプレートエンジンやフレームワークを利用して安全にHTMLとして出力する方法を利用すること強くおすすめします。
</p>

# 検索したい商品キーワードを動的に変更できるようにする

このままでは「ソファ」以外の商品を検索することができません。  
毎回ソースコード変更するわけにもいきません。  
そこでURLのパラメータをうまく使って動的に検索ワードを変更できるようにしましょう。

phpでは `https://localhost?search=xxxxx` という形で渡せば `$_GET` という変数で受け取ることができます。  
この変数はスーパーグローバル変数というものでURLのパラメータ(クエリ)の値を変数として受け取ることができます。
一般的によく使われている方法でGoogle検索でも使われています。

https://www.google.com/search?q=%E9%9D%99%E5%B2%A1 といった `q=xxxx` というクエリを渡しています。  

今回は `search=xxxxxxxxxx` というクエリを受け取って検索ワードとして設定できるようにしましょう。

```php
// 検索したい商品名
$keyword = 'ソファ';
```

api2.phpの上記のコードの部分を以下のように変更してください。

```php
// 検索したい商品名
$keyword = $_GET['search'];
```

変更したら http://localhost/api2.php?search=みかん にアクセスしてみてください。