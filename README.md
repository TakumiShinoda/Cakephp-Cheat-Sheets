# cakephpの使い方
# DebugKitの無効化
  * config/bootstrap.phpの編集
  ```
  - if (Configure::read('debug')) {
  -   Plugin::load('DebugKit', ['bootstrap' => true]);
  - }`
  ```
  ```
  + if (Configure::read('debug')) {
  +   Plugin::load('DebugKit', ['bootstrap' => false]);
  + }
  ```
# defaultレイアウトの無効化
  * `$this->viewBuilder()->layout(false);`を適用させるアクションに記述

# テンプレートを使わない
  * `$this->autoRender = false;`を適用させるアクションに記述

# GETリクエストからクエストリングを取得
  例）http://localhost:xxxx/xxx/?data=hogehoge
  * controllerのアクション内
  ```
    public function xxx(){
      $data = $this->request->getQuery(data);
      $this->log($data);
    }
  ```
  * 出力
  ```
    hogehoge
  ```

# mysqlでの運用
## mysqlでデータベースの作成
  * mysqlに接続：`mysql -uroot`
  * データベース作成：`CREATE DATABASE [データベース名]`
  * データベースに接続：`use [データベース名]`
  * データベースの設定：`GRANT ALL ON *.* to [ユーザ名]@localhost identified by '[パスワード]'; FLUSH PRIVILEGES;`
  * テーブルの作成：`CREATE TABLE [テーブル名（規約で初めは大文字、終わりはs）]`

## config/app.phpの編集
  * config/app.php
  ```
  'Datasources' => [
      'default' => [
          'className' => 'Cake\Database\Connection',
          'driver' => 'Cake\Database\Driver\Mysql',
          'persistent' => false,
          'host' => 'localhost',
          //'port' => 'non_standard_port_number',
          'username' => '[データベースのユーザ名]', // ここを書き換える
          'password' => '[データベースのパスワード]', // ここを書き換える
          'database' => '[データベース名]', // ここを書き換える
          'encoding' => 'utf8',
          'timezone' => 'UTC',
          'flags' => [],
          'cacheMetadata' => true,
          'log' => false,
          ・
          ・
          ・
  ```

## モデルの作成
  * `bin/cake bake model [モデル名（テーブル名と同じ）]Table`

## コントローラーからテーブルのデータを呼ぶ
  * controller
  ```
    // 追記する
    use Cake\ORM\TableRegistry;

    // 呼び出し方（TableRegistry型）
    $tests = TableRegistry::get('[テーブル名]')->find();
    foreach($tests as $test){
      $test->[カラム名];
    }
  ```

# 詰まりどころ
## データベースにsaveするときにprimarykeyがないと言われる場合（非推奨）
  * テーブルにidを追加：`mysql> alter table [テーブル名] add id int`
  * Model/Table/[テーブル名]Table.phpのinitializeメソッドに追記：
  <br>
  `$this->primaryKey('id');`
  * Controllerでsaveするエンティティにidカラムを必ず入れる。
