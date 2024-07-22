# useMotionTemplate

> 여러 모션 값들을 문자열 템플릿을 통해 새로운 하나의 값으로 결합합니다.

`useMotionTemplate`는 다른 모션 값들을 포함하는 [문자열 템플릿](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)으로부터 새로운 [모션 값](https://www.framer.com/motion/motionvalue/)을 생성합니다.

문자열 템플릿 내의 모션 값이 업데이트될 때마다, 새로운 모션 값이 최신 값으로 업데이트됩니다.

## Usage

`useMOtionTemplate`을 Framer Motion에서 import합니다.

```javascript
import { useMotionTemplate } from "framer-motion";
```

`useMotionTemplate`은 "태그된 템플릿 리터럴"입니다. 따라서 일반 함수처럼 호출되지 않고, 문자열 템플릿으로 끝납니다.

```javascript
useMotionTemplate``;
```

이 문자열 템플릿은 텍스트와 다른 모션 값들을 받을 수 있습니다.

```javascript
const x = useMotionValue(100);

// transform.get() === transform(100px)
const transform = useMotionTemplate`transform(${x}px)`;

return <motion.div style={{ transform }} />;
```

반환된 모션 값의 최신 값은 제공된 각 모션 값이 해당 최신 값으로 대체된 문자열 템플릿이 됩니다.

```javascript
const shadowX = useSpring(0);
const shadowY = useMotionValue(0);

const shadow = useMotionTemplate`drop-shadow(${shadowX}px ${shadowY}px 20px rgba(0,0,0,0.3))`;

return <motion.div style={{ filter: shadow }} />;
```
