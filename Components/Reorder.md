# Reorder

> 간단한 컴포넌트 세트로 드래그하여 재정렬하는 효과를 만들 수 있습니다.

`Reorder` 컴포넌트들은 재정렬 가능한 탭이나 할 일 목록과 같이 드래그하여 순서를 변경할 수 있는 목록을 만드는 데 사용할 수 있습니다.

```javascript
import { Reorder } from "framer-motion";
import { useState } from "react";

function List() {
  const [items, setItems] = useState([0, 1, 2, 3]);

  return (
    <Reorder.Group axis="y" values={items} onReorder={setItems}>
      {items.map((item) => (
        <Reorder.Item key={item} value={item}>
          {item}
        </Reorder.Item>
      ))}
    </Reorder.Group>
  );
}
```

## Usage

모든 재정렬 가능한 목록은 `Reorder.Group` 컴포넌트로 감싸집니다.

```javascript
import { Reorder } from "framer-motion";

function List() {
  return <Reorder.Group></Reorder.Group>;
}
```

기본적으로 이는 `<ul>`로 렌더링되지만, `as` prop을 사용하여 변경할 수 있습니다.

```javascript
<Reorder.Group as="ol">
```

`Reorder.Group`에는 `values` prop을 통해 재정렬 가능한 목록의 값 배열을 전달해야 합니다.

또한, `onReorder` 이벤트는 최신 계산된 순서와 함께 발생합니다. 항목들이 재정렬되려면 이 이벤트가 `values` 상태를 업데이트해야 합니다.

```javascript
import { Reorder } from "framer-motion";

function List() {
  const [items, setItems] = useState([0, 1, 2, 3]);

  return <Reorder.Group values={items} onReorder={setItems}></Reorder.Group>;
}
```

각 재정렬 가능한 항목을 렌더링하려면 `Reorder.Item`을 사용하고, `value` prop을 통해 해당 항목이 나타내는 값을 전달합니다.

```javascript
import { Reorder } from "framer-motion";

function List() {
  const [items, setItems] = useState([0, 1, 2, 3]);

  return (
    <Reorder.Group values={items} onReorder={setItems}>
      {items.map((item) => (
        <Reorder.Item key={item} value={item}>
          {item}
        </Reorder.Item>
      ))}
    </Reorder.Group>
  );
}
```

이제 항목들이 드래그될 때, `onReorder`는 새로운 순서와 함께 발생할 것입니다.

### Layout animations

`Reorder.Item` 컴포넌트들은 이미 레이아웃 애니메이션을 수행하도록 구성되어 있습니다. 따라서 재정렬 가능한 목록에 새 항목이 추가되거나 제거되면, 주변 항목들이 자동으로 새로운 위치로 애니메이션 처리됩니다.

### Exit animations

`AnimatePresence`는 항목들이 React 트리에 진입하거나 떠날 때 애니메이션을 적용하기 위해 평소와 같이 사용될 수 있습니다.

```javascript
<AnimatePresence>
  {items.map(item => (
    <Reorder.Item
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}
      key={item}
    />
  ))
</AnimatePresence>
```

### Drag triggers

기본적으로 `Reorder.Item`의 모든 부분이 드래그 가능합니다. [useDragControls](https://www.framer.com/motion/utilities/#usedragcontrols)를 사용하여 드래그 트리거 역할을 할 다른 컴포넌트를 정의할 수 있습니다.

```javascript
import { Reorder, useDragControls } from "framer-motion";

function Item({ value }) {
  const controls = useDragControls();

  return (
    <Reorder.Item value={value} dragListener={false} dragControls={controls}>
      <div
        className="reorder-handle"
        onPointerDown={(e) => controls.start(e)}
      />
    </Reorder.Item>
  );
}
```

### Scrollable lists

`Reorder.Item` 컴포넌트들이 스크롤 가능한 컨테이너 내에 있는 경우, 해당 컨테이너에는 `layoutScroll` prop이 필요합니다. 이를 통해 Framer Motion이 스크롤 오프셋을 정확하게 측정할 수 있습니다.

이 예시에서는 스크롤 가능한 컨테이너가 `Reorder.Group`입니다:

```javascript
<Reorder.Group
  axis="y"
  onReorder={setItems}
  layoutScroll
  style={{ overflowY: "scroll" }}
>
  {items.map((item) => (
    <Item key={item} item={item} />
  ))}
</Reorder.Group>
```

스크롤 가능한 컨테이너가 일반 HTML 컴포넌트인 경우, `layoutScroll`과 함께 `motion` 컴포넌트로 변환해야 합니다.

### z-index

`Reorder.Item`은 현재 드래그 중인 항목에 자동으로 `z-index` 스타일을 설정하여 주변 항목들 위에 표시되도록 합니다.

하지만 `z-index`는 `position !== "static"`인 항목에만 영향을 미칩니다. 따라서 이 효과를 활성화하려면 `Reorder.Item`의 position을 `relative` 또는 `absolute`로 설정해야 합니다.

## Reorder.Group props

**as:string**

`Reorder.Group`가 렌더링할 기본 컴포넌트입니다.
현재는 HTML 요소의 이름만 받지만, 향후에는 HTML을 렌더링하는 모든 React 컴포넌트를 받을 수 있을 것입니다.
기본값은 `"ul"`입니다.

**axis: "x" | "y"**
**Required**

재정렬 감지의 방향입니다.
**NOTE**: 기본적으로 모든 `Reorder.Item` 컴포넌트는 이 축에서만 시각적으로 이동합니다. 두 축 모두에서 시각적 이동을 허용하되 재정렬은 허용하지 않으려면, 자식 `Reorder.Item` 컴포넌트에 `drag` prop을 전달하세요.

**values: any[]**
**Required**

렌더링된 `Reorder.Item` 컴포넌트의 소스로 사용되는 값 배열입니다.

**onReorder(newOrder): void**
**Required**

항목들이 재정렬되었음이 감지될 때 발생하는 콜백입니다. 제공된 `newOrder`는 `values` 상태 업데이트 함수에 전달되어야 합니다.

## Reorder.Item props

`Reorder.Item` 컴포넌트는 다음과 같은 추가적인 prop들과 함께 모든 [motion 컴포넌트](https://www.framer.com/motion/component/#props) prop들을 받습니다:

**as: string**

`Reorder.Item`이 렌더링할 기본 컴포넌트입니다.

현재는 HTML 요소의 이름만 받지만, 향후에는 HTML을 렌더링하는 모든 React 컴포넌트를 받을 수 있을 것입니다.

기본값은 `"li"`입니다.

**value: any**
**Required**
`onReorder`가 호출될 때, 이 값은 새로 정렬된 배열에 전달될 것입니다.
