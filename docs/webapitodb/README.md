# Web APIから取得した情報をデータベースに登録する

次はWebAPIとデータベースを組み合わせて、商品名を入力したら登録出来る機能を作ってみましょう。


## 書籍の登録

実装のサンプルはこのような形です。

以下のファイルをpublicフォルダの「search.php」という名前で作ってみましょう。

```php
<?php
const APP_ID = 'xxxx'; // Application ID
const BASE_API_URL = 'https://app.rakuten.co.jp/services/api/IchibaItem/Search/20170706?applicationId=%s&keyword=%s&formatVersion=2'; // APIのURL

// 検索したい商品名
$search = $_GET['search'] ?? null;

// 検索用の関数の定義
$fnApiSearch = static function ($apiSearchWord): array {
    $url = sprintf(BASE_API_URL, APP_ID, $apiSearchWord);

    // APIからデータを取得する
    $content = file_get_contents($url);

    // JSON形式のデータを連想配列に変換する
    $json = json_decode($content, true, 512, JSON_THROW_ON_ERROR);

    return $json['Items'];
};

$items = match($search) {
    null, '' => [],
    default => $fnApiSearch($search)
};

?>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>欲しい物リスト - 検索</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
</head>
<body>
<main class="container">
  <h1 class="my-3">欲しい物リスト - 検索</h1>
  <div class="my-3">
    <a href="/list.php">欲しい物一覧へ</a>
  </div>
  <div>
    <form action="search.php" method="get">
      <input type="text" name="search" value="<?php echo $search; ?>">
      <input type="submit" value="検索">
  </div>
  <table class="table table-bordered">
    <thead>
    <tr>
      <th style="width: 20%">画像</th>
      <th style="width: 40%">商品名</th>
      <th>価格</th>
      <th></th>
    </tr>
    </thead>
    <tbody>
    <?php foreach ($items as $item): ?>
      <tr>
        <td>
            <?php if (isset($item['mediumImageUrls'][0])) : ?>
              <img src="<?php echo $item['mediumImageUrls'][0]; ?>" alt="">
            <?php endif; ?>
        </td>
        <td>
            <?php echo $item['itemName']; ?>
        </td>
        <td>
            <?php echo $item['itemPrice']; ?>円
        </td>
        <td>
          <a href="<?php echo $item['itemUrl']; ?>" class="btn btn-dark btn-sm" target="_blank">商品情報へ</a>
          <a href="<?php echo sprintf('/register.php?itemCode=%s&itemName=%s&search=%s', $item['itemCode'], $item['itemName'], $search); ?>" class="btn btn-primary btn-sm">欲しい物リストへ登録</a>
        </td>
      </tr>
    <?php endforeach; ?>
    </tbody>
  </table>
  <ul>
      <?php foreach ($items as $item): ?>
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
</main>
</body>
</html>
```

## 実際に登録する処理を実装する

上記処理ができたら、次にデータベースに商品を登録する処理を書いてみましょう。
以下のファイルをpublicフォルダの「register.php」という名前で作ってみましょう。


```php

<?php

/**
 * 商品が登録されているか判定する関数
 */
$hasItem = static function(PDO $pdo, string $itemCode): bool {

    // すでに登録があるか確認する
    $stmt = $pdo->prepare("SELECT * FROM items WHERE item_code = :itemCode"); // クエリの実行
    $stmt->bindParam(':itemCode', $itemCode);
    $stmt->execute();

    return !empty($stmt->fetchAll());
};

/**
 * 商品を登録するための関数
 */
$register = static function(PDO $pdo, string $itemCode, string $name): void {
    $stmt = $pdo->prepare("INSERT INTO items (item_code, name, created_at, updated_at) VALUES (:itemCode, :name, now(), now())");
    $stmt->bindValue(':itemCode', $itemCode);
    $stmt->bindValue(':name', $name);
    $stmt->execute();
};



$itemCode = $_GET['itemCode'] ?? null;
$itemName = $_GET['itemName'] ?? null;

if (null === $itemCode) {
    // itemCodeが指定されてないためsearch.phpにリダイレクト
    header('Location: search.php');
    exit;
}

$search = $_GET['search'] ?? null;

// データベースへの接続
$pdo = new PDO('mysql:host=db;port=3306;dbname=wishlist', 'shizuoka', 'fujiyama'); // データベースへの接続

if (false === $hasItem($pdo, $itemCode)) {
    // 商品が登録されていなければ、商品を登録する
    $register($pdo, $itemCode, $itemName);
}

header('Location: search.php?search=' . $search);
```

## 実際に登録した商品を表示してみましょう。

最後に登録した商品を表示する処理を書いてみましょう。
以下のファイルをpublicフォルダの「list.php」という名前で作ってみましょう。

```php
<?php

// データベースへの接続
$pdo = new PDO('mysql:host=db;port=3306;dbname=wishlist', 'shizuoka', 'fujiyama'); // データベースへの接続

// 登録している商品を取得する
$items = $pdo->query('SELECT * FROM items')->fetchAll();
?>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>欲しい物リスト -　一覧</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
</head>
<body>
<main class="container">
  <h1 class="my-3">欲しい物リスト - 一覧</h1>
  <div class="my-3">
    <a href="/search.php">欲しい物検索へ</a>
  </div>
  <table class="table table-bordered">
    <thead>
    <tr>
      <th>ID</th>
      <th>商品名</th>
      <th>商品画像</th>
      <th>いいね数</th>
      <th>登録日時</th>
      <th>更新日時</th>
    </tr>
    </thead>
    <tbody>
    <?php foreach($items as $item): ?>
      <tr>
        <td><?php echo $item['id']?></td>
        <td><?php echo $item['name']?></td>
        <td><?php echo $item['image']?></td>
        <td><?php echo $item['like_count']?></td>
        <td><?php echo $item['created_at']?></td>
        <td><?php echo $item['updated_at']?></td>
      </tr>
    <?php endforeach; ?>
    </tbody>
  </table>
</main>
</body>
</html>
```