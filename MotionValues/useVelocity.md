# useVelocity

> 다른 모션 값의 속도를 추적하는 모션 값을 생성합니다.

`useVelocity`는 모션 값을 받아 해당 모션 값의 속도로 업데이트되는 새로운 모션 값을 반환합니다.

## Usage

`useVelocity`를 Framer Motion에서 import합니다.

```javascript
import { useVelocity } from "framer-motion";
```

`useVelocity`에 어떤 숫자형 모션 값이든 전달하세요. 그러면 원본 값의 속도로 업데이트되는 새로운 모션 값을 반환할 것입니다.

```javascript
import { useMotionValue, useVelocity } from "framer-motion";

function Component() {
  const x = useMotionValue(0);
  const xVelocity = useVelocity(x);

  useEffect(() => {
    return xVelocity.onChange((latestVelocity) => {
      console.log("Velocity", latestVelocity);
    });
  }, []);

  return <motion.div style={{ x }} />;
}
```

어떤 숫자형 모션 값이든 작동합니다. `useVelocity`에서 반환된 값도 마찬가지입니다.

```javascript
const x = useMotionValue(0);
const xVelocity = useVelocity(x);
const xAcceleration = useVelocity(xVelocity);
```
