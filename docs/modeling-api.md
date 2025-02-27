# BPMN.js モデリング API

このドキュメントでは、BPMN図をプログラムで変更できるBPMN.js[モデリングAPI](https://github.com/bpmn-io/bpmn-js-examples/tree/main/modeling-api)の概要を説明します。

## はじめに

モデリングAPIにアクセスするには、BPMN Viewer/Modelerインスタンスからモデリングサービスを取得する必要があります：

```javascript
// モデリングサービスの取得
const modeling = bpmnModeler.get('modeling');
```

## 基本操作

### 要素の作成

```javascript
// 新しいタスクの作成
modeling.createShape(
  { type: 'bpmn:Task' },
  { x: 100, y: 100 }, // 位置
  parentElement // 省略可能な親要素
);

// 新しい接続の作成
modeling.createConnection(
  sourceElement,
  targetElement,
  { type: 'bpmn:SequenceFlow' },
  parentElement
);
```

### 要素の更新

```javascript
// 要素プロパティの更新
modeling.updateProperties(element, {
  name: '新しいタスク名',
  'custom:property': '値'
});

// 要素ラベルの更新
modeling.updateLabel(element, '新しいラベルテキスト');
```

### 要素の移動

```javascript
// 単一要素の移動
modeling.moveShape(element, { x: 10, y: 20 }, parentElement);

// 複数要素の移動
modeling.moveElements(elements, { x: 10, y: 20 }, parentElement);
```

### 要素の削除

```javascript
// 単一要素の削除
modeling.removeElement(element);

// 複数要素の削除
modeling.removeElements(elements);
```

### 要素のサイズ変更

```javascript
// 要素のサイズ変更
modeling.resizeShape(element, newBounds);
```

## 接続の操作

```javascript
// 接続の作成
const connection = modeling.createConnection(sourceElement, targetElement, {
  type: 'bpmn:SequenceFlow'
}, parentElement);

// 接続の経路点の更新
modeling.updateWaypoints(connection, newWaypoints);

// 接続の再接続
modeling.reconnectStart(connection, sourceElement, newDockingPoint);
modeling.reconnectEnd(connection, targetElement, newDockingPoint);
```

## ラベルの操作

```javascript
// ラベルの作成
modeling.createLabel(element, position, {
  businessObject: businessObject
});

// ラベルの更新
modeling.updateLabel(element, '新しいラベルテキスト', {
  box: { width: 100, height: 50 },
  position: { x: 150, y: 150 }
});
```

## トランザクション

複数のモデリング手順を含む複雑な操作の場合：

```javascript
// トランザクションの開始
const commandStack = bpmnModeler.get('commandStack');

commandStack.execute('transaction.start');

// 複数のモデリング操作の実行
modeling.updateProperties(element1, { name: '最初の要素' });
modeling.updateProperties(element2, { name: '2番目の要素' });
modeling.createConnection(element1, element2, { type: 'bpmn:SequenceFlow' });

// トランザクションの完了
commandStack.execute('transaction.commit');
```

## 元に戻す/やり直し

コマンドスタックを使用して、操作を元に戻したりやり直したりすることができます：

```javascript
const commandStack = bpmnModeler.get('commandStack');

// 最後の操作を元に戻す
commandStack.undo();

// 元に戻した最後の操作をやり直す
commandStack.redo();
```

## イベントリスナー

モデリングイベントをリッスンして変更に反応することができます：

```javascript
const eventBus = bpmnModeler.get('eventBus');

// 要素作成のリッスン
eventBus.on('shape.added', (event) => {
  const element = event.element;
  console.log('要素が作成されました:', element);
});

// 要素更新のリッスン
eventBus.on('element.changed', (event) => {
  const element = event.element;
  console.log('要素が変更されました:', element);
});
```

## 一般的な要素タイプ

* `bpmn:Task` - タスク
* `bpmn:ServiceTask` - サービスタスク
* `bpmn:UserTask` - ユーザータスク
* `bpmn:StartEvent` - 開始イベント
* `bpmn:EndEvent` - 終了イベント
* `bpmn:Gateway` - ゲートウェイ
* `bpmn:ExclusiveGateway` - 排他的ゲートウェイ（XORゲートウェイ）
* `bpmn:ParallelGateway` - 並列ゲートウェイ（ANDゲートウェイ）
* `bpmn:SequenceFlow` - シーケンスフロー

## 追加リソース

* [bpmn-js GitHubリポジトリ](https://github.com/bpmn-io/bpmn-js)
  * このリポジトリのライセンスは、MITライセンスの寛容さを維持しつつ、特定の条件下でソフトウェアを使用することを求めるものです。特に、bpmn.ioのウォーターマークを保持する必要があるという点が、通常のMITライセンスとは大きく異なります。
* [bpmn-jsの例](https://github.com/bpmn-io/bpmn-js-examples)
* [BPMN 2.0仕様](https://www.omg.org/spec/BPMN/2.0/)
* [bpmn-jsでのモデル要素定義](https://github.com/bpmn-io/bpmn-moddle/blob/main/resources/bpmn/json/bpmn.json)
