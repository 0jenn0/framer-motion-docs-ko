# Gestures

## 브라우저를 위한 강력한 제스처 인식 시스템

Motion은 React에서 제공하는 기본 이벤트 리스너 세트를 간단하면서도 강력한 UI 제스처 인식기 세트로 확장합니다.

현재 호버, 탭, 팬, 드래그 제스처 감지를 지원합니다. 각 제스처에는 모션 컴포넌트에 연결할 수 있는 일련의 이벤트 리스너가 있습니다.

## Animation helpers

motion 컴포넌트는 여러 제스처 애니메이션 속성을 제공합니다: `whileHover`, `whileTap`, `whileFocus`, `whileDrag`, `whileInView`. 이들은 제스처가 활성화되어 있는 동안 일시적으로 애니메이션할 대상을 정의할 수 있습니다.

```javascript
<motion.button
  whileHover={{
    scale: 1.2,
    transition: { duration: 1 },
  }}
  whileTap={{ scale: 0.9 }}
/>
```

모든 속성은 애니메이션할 값의 대상으로 설정하거나, `variants` 속성을 통해 정의된 `variants`의 이름으로 설정할 수 있습니다. Variants는 일반적으로 자식 요소로 흘러갑니다.

```javascript
<motion.button whileTap="tap" whileHover="hover" variants={buttonVariants}>
  <svg>
    <motion.path variants={iconVariants} />
  </svg>
</motion.button>
```

motion 컴포넌트는 이러한 `while` 속성들 사이의 상호작용을 자동으로 관리합니다. 예를 들어, 탭 제스처가 활성화된 상태에서 호버가 시작되고 멈추면, 탭 제스처가 우선순위를 받게 되며 두 속성에 모두 정의된 속성은 탭 상태를 유지합니다.
마찬가지로, 두 제스처가 모두 정의되어 있고 탭이 끝나면, 컴포넌트는 탭이 컴포넌트 내부에서 끝나는지 외부에서 끝나는지에 따라 `whileHover`에 정의된 상태나 컴포넌트의 원래 상태로 애니메이션할지를 알게 됩니다

## Propagation

자식 요소는 React의 `Capture` 속성을 사용하여 포인터 이벤트가 부모 `motion` 컴포넌트로 전파되는 것을 막을 수 있습니다.
예를 들어, 자식 요소는 `onPointerDownCapture`에 `e.stopPropagation()`을 전달함으로써 드래그와 탭 제스처 및 이와 관련된 부모의 `while` 애니메이션이 발생하는 것을 막을 수 있습니다.

```javascript
<motion.div whileTap={{ scale: 2 }}>
  <button onPointerDownCapture={(e) => e.stopPropagation()} />
</motion.div>
```

## A note on SVG filters

`while` 헬퍼 속성들은 SVG `filter` 컴포넌트에서는 작동하지 않습니다. 이는 이러한 요소들이 물리적인 존재감이 없어 이벤트를 받지 않기 때문입니다. 제스처에 반응하려면 컴포넌트에 React 상태를 도입하고 물리적인 요소에 리스너를 붙여야 합니다.

```javascript
const MyComponent = () => {
  const [isHovered, setHovered] = useState(false);

  // Simplified example
  return (
    <svg>
      <image
        filter="url(#blur)"
        onMouseEnter={() => setHovered(true)}
        onMouseLeave={() => setHovered(false)}
      />
      <filter id="blur">
        <motion.div
          initial={false}
          animate={{ stdDeviation: isHovered ? 0 : 2 }}
        />
      </filter>
    </svg>
  );
};
```

## Hover

hover 제스처는 포인터가 컴포넌트 위로 이동하거나 떠날 때를 감지합니다.
이는 `onMouseEnter`와 `onMouseLeave`와는 다릅니다. hover는 실제 마우스 이벤트의 결과로만 발생한다는 것이 보장됩니다(터치 입력에서 에뮬레이트된 브라우저 생성 마우스 이벤트와는 대조적입니다).

```javascript
<motion.a
  whileHover={{ scale: 1.2 }}
  onHoverStart={(e) => {}}
  onHoverEnd={(e) => {}}
/>
```

[![Edit framer-motion-2-layout-animations](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/framer-motion-hover-animations-h44jpy?from-embed)

**whileHover:** VariantLabels | TargetAndTransition
hover 제스처가 인식되는 동안 애니메이션할 속성이나 variant 라벨입니다.

```javascript
<motion.div whileHover={{ scale: 1.2 }} />
```

**onHoverStart**(event, info): void
포인터가 컴포넌트 위로 hover하기 시작할 때 실행되는 콜백 함수입니다.

```javascript
<motion.div onHoverStart={() => console.log("Hover starts")} />
```

**onHoverEnd**(event, info): void
포인터가 컴포넌트 위에서 hover를 멈출 때 실행되는 콜백 함수입니다.

```javascript
<motion.div onHoverEnd={() => console.log("Hover ends")} />
```

## Focus

focus 제스처는 CSS :focus-visible 선택자와 동일한 규칙으로 컴포넌트가 포커스를 얻거나 잃을 때를 감지합니다.

일반적으로 이는 input이 어떤 방식으로든 포커스를 받을 때, 그리고 다른 요소들이 접근성 있는 방식(예: 키보드 네비게이션)으로 포커스를 받을 때 발생합니다.

```javascript
<motion.a whileFocus={{ scale: 1.2 }} href="#" />
```

**whileFocus**: VariantLabels | TargetAndTransition

focus 제스처가 인식되는 동안 애니메이션할 속성이나 variant 라벨입니다.

```javascript
<motion.input whileFocus={{ scale: 1.2 }} />
```

## Tap

tap 제스처는 주 포인터(예: 왼쪽 클릭이나 첫 번째 터치 포인트)가 동일한 컴포넌트 위에서 눌렸다가 떼어질 때를 감지합니다.

컴포넌트 위에서 탭이 성공적으로 완료되면 tap 이벤트를 발생시키고, 컴포넌트 외부에서 탭이 끝나면 tapCancel 이벤트를 발생시킵니다.

탭 가능한 컴포넌트가 드래그 가능한 컴포넌트의 자식인 경우, 제스처 중에 포인터가 3픽셀 이상 움직이면 자동으로 탭 제스처를 취소합니다.

[![Edit framer-motion-2-layout-animations](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/framer-motion-press-tap-animations-1igkgz?from-embed)

## Accessibility

tap 이벤트가 있는 요소들은 키보드로 접근 가능합니다.

tap prop이 있는 모든 요소는 포커스를 받을 수 있으며, 포커스된 요소에서 Enter 키를 사용하여 tap 이벤트를 트리거할 수 있습니다.

- Enter 키를 누르면 onTapStart와 whileTap이 트리거됩니다.
- Enter 키를 놓으면 onTap이 트리거됩니다.
- Enter 키를 놓기 전에 요소가 포커스를 잃으면 onTapCancel이 실행됩니다.

**whileTap**: VariantLabels | TargetAndTransition

컴포넌트가 눌려있는 동안 애니메이션할 속성이나 variant 라벨입니다.

```javascript
<motion.div whileTap={{ scale: 0.8 }} />
```

**onTap**(event, info): void
이 요소에서 탭 제스처가 성공적으로 끝났을 때 호출되는 콜백 함수입니다.

> **event**: MouseEvent | TouchEvent | PointerEvent
> 원래의 포인터 이벤트
> **info**: TapInfo
> 디바이스나 페이지를 기준으로 한 point의 x와 y 값을 포함하는 TapInfo 객체

```javascript
function onTapStart(event, info) {
  console.log(info.point.x, info.point.y);
}

<motion.div onTapStart={onTapStart} />;
```

**onTapStart**(event, info): void
이 요소에서 탭 제스처가 시작될 때 호출되는 콜백 함수입니다.

> **event**: MouseEvent | TouchEvent | PointerEvent
> 원래의 포인터 이벤트
> **info**: TapInfo
> 디바이스나 페이지를 기준으로 한 `point`의 `x`와 `y` 값을 포함하는 `TapInfo` 객체

```javascript
function onTapStart(event, info) {
  console.log(info.point.x, info.point.y);
}
<motion.div onTapStart={onTapStart} />;
```

**onTapCancel**(event, info): void
탭 제스처가 이 요소 외부에서 끝났을 때 호출되는 콜백 함수입니다.

> **event**: MouseEvent | TouchEvent | PointerEvent
> 원래의 포인터 이벤트
> **info**: TapInfo
> 디바이스나 페이지를 기준으로 한 `point`의 `x`와 `y` 값을 포함하는 `TapInfo` 객체

```javascript
function onTapCancel(event, info) {
  console.log(info.point.x, info.point.y);
}
<motion.div onTapCancel={onTapCancel} />;
```

## Pan

팬 제스처는 포인터가 컴포넌트 위에서 눌린 후 3픽셀 이상 움직일 때 인식됩니다. 팬 제스처는 포인터가 떼어질 때 종료됩니다.

```javascript
<motion.div onPan={(e, pointInfo) => {}} />
```

**onPan**(event, info): void

이 요소에서 팬 제스처가 인식될 때 실행되는 콜백 함수입니다.

주의: 터치 입력으로 팬 제스처가 올바르게 작동하려면, touch-action CSS 규칙을 사용하여 요소의 x축, y축 또는 양축에서 터치 스크롤을 비활성화해야 합니다.

> **event**: MouseEvent | TouchEvent | PointerEvent
> 원래의 포인터 이벤트
> **info**: PanInfo
> 다음 값들에 대한 `x`와 `y` 값을 포함하는 `PanInfo` 객체:

- point: 디바이스나 페이지를 기준으로 한 상대 위치.
- delta: 마지막 이벤트 이후 이동한 거리.
- offset: 원래 팬 이벤트로부터의 오프셋.
- velocity: 포인터의 현재 속도.

```javascript
function onPan(event, info) {
  console.log(info.point.x, info.point.y);
}
<motion.div onPan={onPan} />;
```

**onPanStart**(event, info): void
이 요소에서 팬 제스처가 시작될 때 실행되는 콜백 함수입니다.

> **event**: MouseEvent | TouchEvent | PointerEvent
> 원래의 포인터 이벤트
> **info**: PanInfo
> (위와 동일한 PanInfo 객체 설명)

```javascript
function onPanStart(event, info) {
  console.log(info.point.x, info.point.y);
}
<motion.div onPanStart={onPanStart} />;
```

**onPanEnd(event, info)**: void
이 요소에서 팬 제스처가 끝날 때 실행되는 콜백 함수입니다.

> **event**: MouseEvent | TouchEvent | PointerEvent
> 원래의 포인터 이벤트
> **info**: PanInfo
> (위와 동일한 PanInfo 객체 설명)

```javascript
function onPanEnd(event, info) {
  console.log(info.point.x, info.point.y);
}
<motion.div onPanEnd={onPanEnd} />;
```

## Drag

드래그 제스처는 팬 제스처의 규칙을 따르지만 포인터의 움직임을 컴포넌트의 x축 및 또는 y축에 적용합니다.

```javascript
<motion.div drag />
```

**whileDrag**: VariantLabels | TargetAndTransition
드래그 제스처가 인식되는 동안 애니메이션할 속성이나 variant 라벨입니다.

```javascript
<motion.div whileDrag={{ scale: 1.2 }} />
```

**drag**: boolean | "x" | "y"
이 요소의 드래그를 활성화합니다. 기본값은 false입니다. true로 설정하면 양방향으로 드래그할 수 있습니다. "x" 또는 "y"로 설정하면 특정 방향으로만 드래그할 수 있습니다.

```javascript
<motion.div drag="x" />
```

**dragConstraints**: false | Partial<BoundingBox2D> | RefObject<Element>
허용된 드래그 가능 영역에 제약을 적용합니다.
선택적으로 top, left, right, bottom 값을 포함하는 객체를 받을 수 있으며, 이 값들은 픽셀 단위로 측정됩니다. 이는 드래그 가능한 컴포넌트의 각 모서리의 거리를 정의합니다.

또는 React의 useRef 훅으로 생성된 다른 컴포넌트에 대한 ref를 받을 수 있습니다. 이 ref는 드래그 가능한 컴포넌트의 dragConstraints prop과 제약 조건으로 사용하려는 컴포넌트의 ref에 모두 전달되어야 합니다.

```javascript
// 픽셀 단위로
<motion.div drag="x" dragConstraints={{ left: 0, right: 300 }} />;

// 다른 컴포넌트에 대한 ref로
const MyComponent = () => {
  const constraintsRef = useRef(null);
  return (
    <motion.div ref={constraintsRef}>
      <motion.div drag dragConstraints={constraintsRef} />
    </motion.div>
  );
};
```

## dragSnapToOrigin: boolean

만약 true라면, 드래그 가능한 요소는 놓았을 때 중심/원점으로 다시 애니메이션됩니다.

**dragElastic**: DragElastic
제약 조건 밖으로 허용되는 이동의 정도입니다. 0 = 이동 없음, 1 = 완전한 이동.
기본값은 0.5입니다. 이동을 비활성화하려면 false로 설정할 수도 있습니다.
top/right/bottom/left 객체를 전달하여 각 제약 조건별로 개별 값을 설정할 수 있습니다. 누락된 값은 0으로 설정됩니다.

```javascript
<motion.div drag dragConstraints={{ left: 0, right: 300 }} dragElastic={0.2} />
```

**dragMomentum**: boolean

드래그가 끝났을 때 팬 제스처의 모멘텀을 컴포넌트에 적용합니다. 기본값은 true입니다.

```javascript
<motion.div
  drag
  dragConstraints={{ left: 0, right: 300 }}
  dragMomentum={false}
/>
```

**dragTransition**: InertiaOptions
드래깅 관성 매개변수를 변경할 수 있게 합니다. 드래그 가능한 프레임을 놓을 때, inertia 타입의 애니메이션이 시작됩니다. 이 애니메이션은 드래깅 속도를 기반으로 합니다. 이 속성을 통해 이를 커스터마이즈할 수 있습니다.

```javascript
<motion.div drag dragTransition={{ bounceStiffness: 600, bounceDamping: 10 }} />
```

**dragPropagation**: boolean
드래그 제스처가 자식 컴포넌트로 전파되도록 허용합니다. 기본값은 false입니다.

```javascript
<motion.div drag="x" dragPropagation />
```

**dragControls**: DragControls
일반적으로 드래그는 컴포넌트를 누르고 움직이는 것으로 시작됩니다. 하지만 일부 사용 사례에서는, 예를 들어 비디오 스크러버의 임의의 지점을 클릭하는 경우, 드래그 가능한 컴포넌트가 아닌 다른 컴포넌트에서 드래그를 시작하고 싶을 수 있습니다.
useDragControls 훅을 사용하여 dragControls를 생성함으로써, 이를 드래그 가능한 컴포넌트의 dragControls prop에 전달할 수 있습니다. 이는 다른 컴포넌트의 포인터 이벤트에서 드래그를 시작할 수 있는 start 메서드를 노출합니다.

```javascript
const dragControls = useDragControls();

function startDrag(event) {
  dragControls.start(event, { snapToCursor: true });
}

return (
  <>
    <div onPointerDown={startDrag} />
    <motion.div drag="x" dragControls={dragControls} />
  </>
);
```

주의: dragControls를 설정함으로써 드래그 제스처의 시작을 제어하게 되므로, dragListener={false}를 설정하여 드래그 가능한 요소를 시작자로 비활성화할 수 있습니다.

**dragListener**: boolean
이벤트 리스너에서 드래그 제스처를 트리거할지 여부를 결정합니다. dragControls를 전달하는 경우, 이를 false로 설정하면 드래그 가능한 요소의 pointerdown 이벤트가 아닌 컨트롤을 통해서만 드래그를 시작할 수 있습니다.

**onDrag(event, info)**: void
컴포넌트가 드래그될 때 실행되는 콜백 함수입니다.
event: MouseEvent | TouchEvent | PointerEvent
info: PanInfo

```javascript
<motion.div
  drag
  onDrag={(event, info) => console.log(info.point.x, info.point.y)}
/>
```

**onDragStart**(event, info): void
드래그가 시작될 때 실행되는 콜백 함수입니다.
event: MouseEvent | TouchEvent | PointerEvent
info: PanInfo

```javascript
<motion.div
  drag
  onDragStart={(event, info) => console.log(info.point.x, info.point.y)}
/>
```

**onDragEnd**(event, info): void
드래그가 끝날 때 실행되는 콜백 함수입니다.
event: MouseEvent | TouchEvent | PointerEvent
info: PanInfo

```javascript
<motion.div
  drag
  onDragEnd={(event, info) => console.log(info.point.x, info.point.y)}
/>
```

**onDirectionLock(axis)**: void
드래그 방향이 결정될 때 실행되는 콜백 함수입니다.
axis: "x" | "y"

```javascript
<motion.div
  drag
  dragDirectionLock
  onDirectionLock={(axis) => console.log(axis)}
/>
```
