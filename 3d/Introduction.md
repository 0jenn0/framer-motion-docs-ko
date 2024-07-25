# 3D: Introduction

## Overview

> Framer Motion 3D는 [React Three Fiber](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction)를 위한 간단하면서도 강력한 애니메이션 라이브러리입니다.

이 가이드는 Motion 3D를 사용하여 애니메이션을 만드는 데 도움을 줄 것입니다. 단, [Framer Motion](https://www.framer.com/motion/)과 [React Three Fiber](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction)의 기본 사항을 알고 있다고 가정합니다.

## Installation

Framer Motion 3D는 [Three.js](https://threejs.org/)와 [React Three Fiber (R3F)](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction) 라이브러리를 기반으로 구축되었습니다. npm에서 세 가지 모두 설치하세요:

```javascript
npm install three@0.137.0 @react-three/fiber@8.2.2 framer-motion-3d@11.2.0
```

**Warning**: Framer Motion 3D는 React 18과만 호환되며, React 19 호환성에 대한 일정은 아직 없습니다.

## Usage

### `motion` 컴포넌트

모든 R3F 컴포넌트에 대해 `motion` 등가물이 있습니다 "framer-motion-3d"에서 `motion`을 가져오세요:

```javascript
import { motion } from "framer-motion-3d";
```

그리고 R3F 컴포넌트 대신 사용하세요:

```javascript
<motion.pointLight animate={{ x: 2 }} />
```

### Animation

Motion 3D는 `initial`과 [animate]() props, variants, 그리고 명령형 `useAnimation` 컨트롤을 포함하여 일반적인 모든 애니메이션 옵션을 지원합니다.

```javascript
const variants = {
  hidden: { opacity: 0 },
  visible: { opacity: 1 },
};

return (
  <motion.meshStandardMaterial
    initial="hidden"
    animate="visible"
    variants={variants}
  />
);
```

현재, variants는 DOM과 3D 세계 사이에 자동으로 전달될 수 없지만, 비슷한 결과를 얻기 위해 상태를 공유할 수 있습니다:

```javascript
// index.js
import { motion } from "framer-motion";
import { Scene } from "./scene";

export function App() {
  const [isHovered, setIsHovered] = useState(false);

  return (
    <motion.div
      whileHover={{ scale: 1.2 }}
      onHoverStart={() => setIsHovered(true)}
      onHoverEnd={() => setIsHovered(true)}
    >
      <Scene isHovered={isHovered} />
    </motion.div>
  );
}

// scene.js
import { Canvas } from "@react-three/fiber";
import { motion } from "framer-motion-3d";

export function Scene({ isHovered }) {
  return (
    <Canvas>
      <motion.group animate={isHovered ? "hover" : "rest"}>
        <motion.mesh variants={{ hover: { z: 1 } }} />
      </motion.group>
    </Canvas>
  );
}
```

### Supported values

3D `motion` 컴포넌트는 2D 등가물과 대부분 동일한 변환을 지원합니다:

- `x`, `y` 그리고 `z`
- `scale`, `scaleX`, `scaleY` 그리고 `scaleZ`
- `rotateX`, `rotateY` 그리고 `rotateZ`

추가로, `color`와 `opacity`는 `meshStandardMaterial`과 같이 이를 지원하는 3D 프리미티브에서 지원되며, 가까운 미래에 더 많은 값들에 대한 지원이 추가될 예정입니다.

### Gestures

3D `motion` 컴포넌트는 물리적 존재감이 있는 R3F(예: `mesh`)에서 호버와 탭 [gestures](https://www.framer.com/motion/gestures/)를 지원합니다.

```javascript
<motion.mesh
  whileHover={{ scale: 1.1 }}
  whileTap={{ scale: 0.9 }}
  onHoverStart={() => console.log("hover start")}
  onTap={() => console.log("tapped!")}
/>
```

### `MotionValue`

`MotionValue`는 React의 렌더 생명주기 외부에서 애니메이션 값의 상태와 속도를 추적하는 데 사용됩니다.

3D `motion` 컴포넌트에서 `MotionValue`는 R3F 속성을 통해 주입됩니다:

```javascript
import { useMotionValue, useTransform } from "framer-motion";
import { motion } from "framer-motion-3d";

export function Box() {
  const x = useMotionValue(0);
  const scaleZ = useTransform(x, (v) => v / 100);

  return (
    <motion.mesh position-x={x} scale={[1, 1, scaleZ]} animate={{ x: 100 }} />
  );
}
```

### Layout animations

레이아웃 애니메이션에 관련된 이미지, 따라서 3D 장면은 스케일 왜곡을 보일 수 있습니다. `LayoutCamera`와 `LayoutOrthographicCamera` 컴포넌트를 사용하면 이 왜곡을 수정할 수 있으며 3D 장면을 자연스럽게 레이아웃 애니메이션에 통합할 수 있습니다.
