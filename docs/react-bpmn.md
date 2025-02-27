# react-bpmn

このドキュメントでは、Reactアプリケーションで[bpmn-js](https://github.com/bpmn-io/bpmn-js)を使用するための[react-bpmn](https://github.com/bpmn-io/react-bpmn)ライブラリの使い方を説明します。

## 概要

react-bpmnは、ReactアプリケーションでBPMNダイアグラムを表示するためのシンプルなラッパーコンポーネントを提供します。このコンポーネントを使用することで、BPMNダイアグラムの表示や操作が容易になります。

## インストール

```bash
npm install react-bpmn
```

## 基本的な使用方法

```jsx
import React from 'react';
import BpmnViewer from 'react-bpmn';

function App() {
  return (
    <div className="App">
      <BpmnViewer url="/diagram.bpmn" />
    </div>
  );
}

export default App;
```

## プロパティ

`BpmnViewer`コンポーネントは以下のプロパティをサポートしています：

- `url`: BPMNファイルのURL
- `diagramXML`: 直接BPMNのXML文字列を指定
- `importDone`: インポートが完了したときに呼び出されるコールバック関数
- `onShown`: ダイアグラムが表示されたときに呼び出されるコールバック関数
- `onLoading`: ダイアグラムがロード中のときに呼び出されるコールバック関数
- `onError`: エラーが発生したときに呼び出されるコールバック関数

## イベントハンドリング

```jsx
import React from 'react';
import BpmnViewer from 'react-bpmn';

function App() {
  const handleError = (err) => {
    console.error('Failed to load diagram', err);
  };

  const handleImport = (err) => {
    if (err) {
      console.error('Import error', err);
    } else {
      console.log('Import successful');
    }
  };

  const handleShown = () => {
    console.log('Diagram shown');
  };

  return (
    <div className="App">
      <BpmnViewer 
        url="/diagram.bpmn"
        onError={handleError}
        importDone={handleImport}
        onShown={handleShown}
      />
    </div>
  );
}

export default App;
```

## カスタマイズ

bpmn-jsのビューワーインスタンスにアクセスする場合、`onLoading`コールバックを使用できます：

```jsx
<BpmnViewer
  url="/diagram.bpmn"
  onLoading={(viewer) => {
    // ビューワーインスタンスに直接アクセスして設定を変更
    viewer.get('canvas').zoom('fit-viewport');
  }}
/>
```

## 実装例

完全な実装例は以下のようになります：

```jsx
import React, { useState } from 'react';
import BpmnViewer from 'react-bpmn';

function BpmnViewerComponent() {
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  return (
    <div>
      <h1>BPMN Viewer</h1>
      {loading && <p>Loading diagram...</p>}
      {error && <p style={{ color: 'red' }}>Error: {error.message}</p>}
      
      <div style={{ height: '500px', border: '1px solid #ccc' }}>
        <BpmnViewer
          url="/resources/diagram.bpmn"
          onLoading={() => setLoading(true)}
          onShown={() => setLoading(false)}
          onError={(err) => {
            setError(err);
            setLoading(false);
          }}
        />
      </div>
    </div>
  );
}

export default BpmnViewerComponent;
```

## スタイリング

react-bpmnコンポーネントは、デフォルトでは親要素の100%のサイズになります。特定のサイズを指定したい場合は、親要素にスタイルを適用してください：

```jsx
<div style={{ height: '500px', width: '100%' }}>
  <BpmnViewer url="/diagram.bpmn" />
</div>
```

## 参考

* [react-bpmn](https://github.com/bpmn-io/react-bpmn)
* [bpmn-js](https://github.com/bpmn-io/bpmn-js)
* [bpmn-io](https://bpmn.io/)