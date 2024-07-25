# MotionCanvas

> Framer Motion과 Framer Motion 3D를 연결하기 위한 Canvas 컴포넌트입니다.

React Three Fiber(R3F)는 3D 장면을 설정하기 위해 [Canvas component](https://docs.pmnd.rs/react-three-fiber/api/canvas)를 사용합니다. 이 컴포넌트를 사용하면 부모 컴포넌트와의 컨텍스트가 끊어집니다.

Framer Motion 3D 컨텍스트를 Framer Motion과 연결하기 위해, 예를 들어 기본 전환을 공유하거나 [LayoutCamera](https://www.framer.com/motion/layoutcamera/)를 레이아웃 애니메이션과 연결하기 위해, `MotionCanvas` 컴포넌트를 대신 사용할 수 있습니다.

```javascript
import { MotionConfig, motion } from "framer-motion";
import { MotionCanvas, motion as motion3d } from "framer-motion-3d";

export function App() {
  return (
    <MotionConfig transition={{ type: "spring" }}>
      <motion.div animate={{ scale: 2 }}>
        <MotionCanvas>
          <motion3d.boxGeometry animate={{ x: 1 }} />
        </MotionCanvas>
      </motion.div>
    </MotionConfig>
  );
}
```

`MotionCanvas` 컴포넌트는 R3F의 `Canvas` 컴포넌트와 동일한 모든 속성을 공유합니다. 다만 `resize` 속성은 제외되는데, 이는 `MotionCanvas`가 Framer Motion의 레이아웃 애니메이션과 동기화하기 위해 자체적인 리사이즈 옵션을 구현하기 때문입니다.
