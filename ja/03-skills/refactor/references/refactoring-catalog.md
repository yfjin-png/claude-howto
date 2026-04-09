# リファクタリング カタログ

Martin Fowlerの *Refactoring*（第2版）からキュレートされたリファクタリング テクニックのカタログ。各リファクタリングには、モチベーション、段階的なメカニクス、例が含まれます。

> 「リファクタリングはそのメカニクスによって定義されます — あなたが変更を実行するために従う正確なステップのシーケンス。」— Martin Fowler

---

## このカタログの使用方法

1. **コード スメルを特定する** コード スメル リファレンスを使用
2. **一致するリファクタリングを見つける** このカタログ内
3. **メカニクスに従う** ステップ バイ ステップ
4. **各ステップ後にテストする** 動作が保存されていることを確認

**黄金のルール**：任意のステップが10分以上かかる場合、より小さなステップに分割します。

---

## 最も一般的なリファクタリング

### メソッドを抽出

**使用する場合**：ロング メソッド、重複コード、概念に名前を付ける必要

**モチベーション**：コード フラグメントをメソッドに変換します。その目的を説明する名前。

**メカニクス**：
1. 何をするかで名前付けされた新しいメソッドを作成します（どのように）
2. コード フラグメントを新しいメソッドにコピー
3. フラグメントで使用されるローカル変数をスキャン
4. ローカル変数をパラメーターとして渡す（またはメソッドで宣言）
5. 戻り値を適切に処理
6. 元のフラグメントを新しいメソッドの呼び出しに置き換える
7. テスト

**前：**
```javascript
function printOwing(invoice) {
  let outstanding = 0;

  console.log("***********************");
  console.log("**** Customer Owes ****");
  console.log("***********************");

  // 未払い残高の計算
  for (const order of invoice.orders) {
    outstanding += order.amount;
  }

  // 詳細を出力
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);
}
```

**後：**
```javascript
function printOwing(invoice) {
  printBanner();
  const outstanding = calculateOutstanding(invoice);
  printDetails(invoice, outstanding);
}

function printBanner() {
  console.log("***********************");
  console.log("**** Customer Owes ****");
  console.log("***********************");
}

function calculateOutstanding(invoice) {
  return invoice.orders.reduce((sum, order) => sum + order.amount, 0);
}

function printDetails(invoice, outstanding) {
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);
}
```

---

### インラインメソッド

**使用する場合**：メソッド本体は名前と同じくらい明確、過度なデリゲーション

**モチベーション**：メソッドが値を追加しない場合、不要な間接参照を削除します。

**メカニクス**：
1. メソッドが多態的でないことを確認
2. メソッドへのすべての呼び出しを見つける
3. 各呼び出しをメソッド本体に置き換える
4. 各置換後にテスト
5. メソッド定義を削除

**前：**
```javascript
function getRating(driver) {
  return moreThanFiveLateDeliveries(driver) ? 2 : 1;
}
```

---

### パラメーター オブジェクトを導入

**使用する場合**：一緒に移動するデータの多くのパラメーター

**モチベーション**：密接に関連するパラメーターを1つのオブジェクトに整理します。

**メカニクス**：
1. 新しいクラス/オブジェクトを作成してパラメーターをホスト
2. メソッド シグネチャを新しいオブジェクトを受け入れるように変更
3. メソッド内でアクセスを置き換える
4. テスト

**前：**
```javascript
function createUser(firstName, lastName, email, phone) {
  // ...
}
```

**後：**
```javascript
function createUser(personalInfo) {
  const { firstName, lastName, email, phone } = personalInfo;
  // ...
}
```

---

## その他の一般的なリファクタリング

**プルアップ メソッド** — スーパークラスへ共有メソッドを移動

**プッシュ ダウン メソッド** — サブクラスにメソッドを移動

**メソッドを移動** — 別のクラスにメソッドを移動

**フィールドを移動** — 別のクラスにフィールドを移動

**クラスを抽出** — クラスを複数のクラスに分割

**インラインクラス** — 小さいクラスをその呼び出し元に統合

**階層を折りたたむ** — 不要な継承レベルを削除

**条件を多態性で置き換える** — ポリモーフィズムで条件をリファクタリング

---

## 注意

このカタログは簡潔です。完全な詳細については、Martin Fowlerの *Refactoring* 書を参照してください。

---

## 最も有用なリファクタリング

実践的な経験から、これらは最も頻繁に使用されます：

1. **メソッドを抽出** — 最も基本的で多く使用
2. **メソッドをインライン化** — 不要な委任を削除
3. **パラメーター オブジェクトを導入** — パラメーター リストを縮小
4. **メソッドを移動** — 高い内聚性のためにコードを整理
5. **クラスを抽出** — 責任を分離
6. **条件を多態性で置き換える** — OOPをさらに活用
7. **デッド コードを削除** — 不要なコードを整理
