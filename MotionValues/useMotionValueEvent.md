# useMotionValueEvent

> React 컴포넌트에서 모션 값 이벤트를 구독합니다.

`useMotionValueEvent`는 React 컴포넌트의 생명주기 전반에 걸쳐 MotionValue 이벤트 핸들러를 관리합니다.

```javascript
function Component() {
  const x = useMotionValue(0);

  useMotionValueEvent(x, "animationStart", () => {
    console.log("animation started on x");
  });

  useMotionValueEvent(x, "change", (latest) => {
    console.log("x changed to", latest);
  });

  return <motion.div style={{ x }} />;
}
```

컴포넌트가 언마운트될 때, 이벤트 핸들러는 자동으로 안전하게 구독 해제됩니다.

## Usage

Framer Motion에서 import합니다.

```javascript
import { useMotionValueEvent } from "framer-motion";
```

`MotionValue`에 이벤트 리스너를 추가하려면 값, 이벤트 이름, 그리고 콜백을 제공하세요.

```javascript
useMotionValueEvent(value, "change", (latest) => {
  console.log(latest);
});
```

- 사용 가능한 이벤트는 다음과 같습니다:
  - change
  - animationStart
  - animationComplete
  - animationCancel

`"change"` 이벤트는 `MotionValue`의 최신 값을 제공받습니다.
`useMotionValueEvent`는 `MotionValue.on`의 헬퍼 메소드입니다. 따라서 여전히 `useEffect` 내에서 직접 이벤트를 설정할 수 있지만, 올바르게 정리되도록 `useEffect`의 반환 함수에 `on` 구독 해제 함수를 전달하는 것을 잊지 마세요.

```javascript
useEffect(() => {
  const doSomething = () => {};

  const unsubX = x.on("change", doSomething);
  const unsubY = y.on("change", doSomething);

  return () => {
    unsubX();
    unsubY();
  };
}, [x, y]);
```
