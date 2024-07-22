# useDragControls

> 모션 컴포넌트에서 수동으로 드래그를 시작/중지합니다.

일반적으로 드래깅은 `drag prop`을 가진 `motion` 컴포넌트를 누른 다음 포인터를 움직임으로써 시작됩니다.

일부 사용 사례, 예를 들어 비디오 스크러버의 임의의 지점을 클릭하는 경우, 우리는 드래그 가능한 컴포넌트가 아닌 다른 컴포넌트에서 드래깅을 시작하고 싶을 수 있습니다.

`useDragControls`를 사용하면 어떤 포인터 이벤트에서든 수동으로 드래깅을 시작할 수 있는 컨트롤 세트를 만들 수 있습니다

## Usage

`useDragControls`를 framer-motion에서 import합니다.

```javascript
import { motion, useDragControls } from "framer-motion";
```

훅을 호출하여 컨트롤을 생성한 다음, 이를 드래그 가능한 `motion` 컴포넌트에 전달합니다.

```javascript
function Component() {
  const controls = useDragControls();

  return (
    <>
      <div />
      <motion.div drag="x" dragControls={controls} />
    </>
  );
}
```

이제 우리는 `start` 메서드를 통해 다른 어떤 요소의 `onPointerDown` 이벤트에서도 드래그 세션을 시작할 수 있습니다.

```javascript
const controls = useDragControls();

function startDrag(event) {
  controls.start(event);
}

return (
  <>
    <div onPointerDown={startDrag} />
    <motion.div drag="x" dragControls={controls} />
  </>
);
```

## Touch support

터치 스크린을 지원하려면 트리거 요소에 `touch-action: none` 스타일을 적용해야 합니다.

```javascript
<div onPointerDown={startDrag} style={{ touchAction: "none" }} />
```

## Snap to cursor

기본적으로 수동 드래그 제스처는 포인터의 변화만 `motion` 컴포넌트에 적용합니다.

`start` 메서드에 `snapToCursor: true`를 전달하여 `motion` 컴포넌트가 즉시 커서로 스냅되도록 할 수도 있습니다.

```javascript
controls.start(event, { snapToCursor: true });
```

## Blocking automatic drag

이 구성에서는 `motion` 컴포넌트가 여전히 자체적으로 `pointerdown` 이벤트를 받을 때 자동으로 드래그 제스처를 시작합니다.

`dragListener={false}` prop을 전달하여 이 동작을 중지할 수 있습니다.

```javascript
<motion.div drag dragListener={false} dragControls={controls} />
```
