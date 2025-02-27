# React BPMNの実装例

このドキュメントでは、Reactアプリケーションでreact-bpmnを使用した詳細な実装例を紹介します。

## 基本的な実装例

以下は、Create React Appで作成したプロジェクトにreact-bpmnを実装する基本的な例です。

### インストール

```bash
# Reactプロジェクトを作成
npx create-react-app react-bpmn-example

# 必要なパッケージをインストール
cd react-bpmn-example
npm install react-bpmn bpmn-js
```

### App.jsの実装

```jsx
import React, { useState } from 'react';
import BpmnViewer from 'react-bpmn';
import './App.css';

// サンプルのBPMNダイアグラムをインポート
import sampleBpmn from './resources/diagram.bpmn';

function App() {
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  const handleLoading = () => {
    setIsLoading(true);
    setError(null);
  };

  const handleError = (err) => {
    setIsLoading(false);
    setError(err);
  };

  const handleShown = () => {
    setIsLoading(false);
  };

  const handleImportDone = (err, warnings) => {
    if (err) {
      console.error('Import error:', err);
    }
    if (warnings && warnings.length) {
      console.warn('Import warnings:', warnings);
    }
  };

  return (
    <div className="App">
      <header className="App-header">
        <h1>React BPMN Viewer Example</h1>
      </header>
      <main>
        {isLoading && <div className="loading-indicator">Loading diagram...</div>}
        {error && <div className="error-message">Error: {error.message}</div>}
        
        <div className="diagram-container">
          <BpmnViewer
            url={sampleBpmn}
            onLoading={handleLoading}
            onError={handleError}
            onShown={handleShown}
            importDone={handleImportDone}
          />
        </div>
      </main>
    </div>
  );
}

export default App;
```

### App.cssのスタイル

```css
.App {
  text-align: center;
}

.App-header {
  background-color: #282c34;
  padding: 20px;
  color: white;
}

.diagram-container {
  height: 70vh;
  border: 1px solid #ccc;
  margin: 20px;
}

.loading-indicator {
  padding: 10px;
  background-color: #f0f0f0;
  border-radius: 4px;
  margin: 10px;
}

.error-message {
  padding: 10px;
  background-color: #ffebee;
  color: #d32f2f;
  border-radius: 4px;
  margin: 10px;
}
```

## 高度な実装例

以下は、BPMNダイアグラムの操作や相互作用を追加した高度な実装例です。

### 拡張されたBPMNビューワー

```jsx
import React, { useState, useEffect, useRef } from 'react';
import BpmnViewer from 'react-bpmn';

function EnhancedBpmnViewer({ url, onSelectElement }) {
  const [loading, setLoading] = useState(true);
  const viewerRef = useRef(null);
  
  // ビューワーインスタンス設定
  const handleLoading = (viewer) => {
    viewerRef.current = viewer;
    setLoading(true);
    
    // 要素クリックイベントリスナーを追加
    const eventBus = viewer.get('eventBus');
    eventBus.on('element.click', (e) => {
      const { element } = e;
      if (onSelectElement) {
        onSelectElement(element);
      }
    });
  };
  
  // ズームコントロール
  const handleZoom = (value) => {
    if (viewerRef.current) {
      const canvas = viewerRef.current.get('canvas');
      
      if (value === 'fit') {
        canvas.zoom('fit-viewport');
      } else {
        canvas.zoom(canvas.zoom() + value);
      }
    }
  };
  
  // ダイアグラム回転
  const handleRotate = (angle) => {
    if (viewerRef.current) {
      const canvas = viewerRef.current.get('canvas');
      const currentAngle = canvas.viewbox().angle || 0;
      canvas.viewbox({
        angle: currentAngle + angle
      });
    }
  };
  
  return (
    <div className="enhanced-bpmn-viewer">
      {loading && <div className="loading">Loading diagram...</div>}
      
      <div className="diagram-container">
        <BpmnViewer
          url={url}
          onLoading={handleLoading}
          onShown={() => setLoading(false)}
        />
      </div>
      
      <div className="controls">
        <button onClick={() => handleZoom(0.1)}>+</button>
        <button onClick={() => handleZoom(-0.1)}>-</button>
        <button onClick={() => handleZoom('fit')}>Fit</button>
        <button onClick={() => handleRotate(90)}>↻</button>
        <button onClick={() => handleRotate(-90)}>↺</button>
      </div>
    </div>
  );
}

// 使用例
function App() {
  const [selectedElement, setSelectedElement] = useState(null);
  
  const handleElementSelect = (element) => {
    setSelectedElement({
      id: element.id,
      type: element.type,
      name: element.businessObject.name || '名称なし'
    });
  };
  
  return (
    <div className="app">
      <EnhancedBpmnViewer
        url="/resources/diagram.bpmn"
        onSelectElement={handleElementSelect}
      />
      
      {selectedElement && (
        <div className="element-info">
          <h3>選択された要素:</h3>
          <p>ID: {selectedElement.id}</p>
          <p>タイプ: {selectedElement.type}</p>
          <p>名前: {selectedElement.name}</p>
        </div>
      )}
    </div>
  );
}

export default App;
```

## XMLを文字列として指定する例

```jsx
import React from 'react';
import BpmnViewer from 'react-bpmn';

function XmlStringExample() {
  const diagramXML = `<?xml version="1.0" encoding="UTF-8"?>
<bpmn:definitions xmlns:bpmn="http://www.omg.org/spec/BPMN/20100524/MODEL" id="Definitions_1" targetNamespace="http://bpmn.io/schema/bpmn">
  <bpmn:process id="Process_1" isExecutable="false">
    <bpmn:startEvent id="StartEvent_1" name="開始" />
  </bpmn:process>
</bpmn:definitions>`;

  return (
    <div style={{ height: '500px' }}>
      <BpmnViewer diagramXML={diagramXML} />
    </div>
  );
}

export default XmlStringExample;
```

## 参考リソース

- [react-bpmn GitHub](https://github.com/bpmn-io/react-bpmn)
- [bpmn-js ドキュメント](https://github.com/bpmn-io/bpmn-js/tree/master/docs)
- [bpmn-io 公式サイト](https://bpmn.io/)
