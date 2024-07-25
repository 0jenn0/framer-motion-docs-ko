# LayoutOrthographicCamera

> 직교 카메라로, Framer Motion의 레이아웃 애니메이션과 통합됩니다.

Framer Motion의 [layout animations](https://www.framer.com/motion/animation/#layout-animations)은 최상의 성능을 위해 `transform` 스타일을 통해 작동합니다. HTML 요소가 `scale`로 변형될 때, 왜곡이 발생할 수 있습니다.

`LayoutOrthographicCamera`를 사용하면, CSS `transform`이 적용될 때 레이아웃 애니메이션 전반에 걸쳐 올바르게 보이도록 3D 장면을 미리 왜곡할 수 있습니다.

## Usage

`LayoutOrthographicCamera`를 구현하려면 먼저 `@react-three/fiber`의 `Canvas`를 `framer-motion-3d`의 `MotionCanvas`로 대체해야 합니다.

그런 다음 [MotionCanvas](https://www.framer.com/motion/motioncanvas/) 내부 어디에서나 `LayoutOrthographicCamera`를 추가할 수 있습니다:

```javascript
// index.js
import { motion } from "framer-motion";
import { Scene } from "./scene";

const fullscreen = { position: "fixed", inset: 0 };

export function App({ isFullscreen }) {
  return (
    <motion.div layout style={isFullscreen ? fullscreen : {}}>
      <Scene />
    </motion.div>
  );
}

// scene.js
import { MotionCanvas, LayoutCamera } from "framer-motion-3d";

export function Scene() {
  <MotionCanvas>
    <LayoutOrthographicCamera position={[0, 0, 5]} zoom={40} />
    <Box />
  </MotionCanvas>;
}
```

부모 HTML `motion` 컴포넌트가 레이아웃 애니메이션을 수행할 때, 렌더링된 이미지가 자동으로 보정됩니다.

## Props

내부적으로 `LayoutOrthographicCamera`는 `<motion.orthographicCamera />` 컴포넌트를 렌더링합니다. 따라서 ``position`과 `zoom`과 같은 일반적인 React Three Fiber 속성뿐만 아니라 `initial`과 `animate`와 같은 `motion` 속성도 모두 받을 수 있습니다.

```javascript
<LayoutCamera
  position={[0, 0, 5]}
  zoom={20}
  animate={{ zoom: 100 }}
  transition={{ duration: 2 }}
/>
```
