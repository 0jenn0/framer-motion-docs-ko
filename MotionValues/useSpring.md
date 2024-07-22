# useSpring

> spring으로 목표값까지 애니메이션되는 모션 값입니다.

`useSpring`은 스프링 애니메이션을 사용하여 최신 목표값으로 애니메이션되는 모션 값을 생성합니다. 목표값은 수동으로 `.set`을 통해 설정하거나, 다른 모션 값을 전달하여 자동으로 설정할 수 있습니다.

## Usage

Frmaer Motion에서 import합니다.

```javascript
import { useSpring } from "framer-motion";
```

## Create

`useSpring`은 숫자로 초기화될 수 있습니다.

```javascript
const spring = useSpring(0);
```

또는 숫자형 모션 값을 전달

```javascript
const x = useMotionValue(0);
const spring = useSpring(x);
```

모션 값을 전달함으로써, 반환된 스프링은 자동으로 해당 모션 값의 최신 값으로 애니메이션됩니다.

숫자를 전달하면, 스프링은 `set`에 전달된 최신 값으로 애니메이션됩니다.

```javascript
const x = useSpring(0);

useEffect(() => {
  x.set(target);
}, [target]);

return <motion.div style={{ x }} />;
```

**NOTE**: 부수 효과로, 모션 값은 항상 주 렌더 함수에서 비동기적으로만 설정되어야 합니다. 예를 들어 effect나 이벤트 핸들러 내에서 설정해야 합니다.

## Config

`useSpring`은 일반적인 스프링 전환 옵션으로 구성할 수 있습니다.

```javascript
useSpring(x, { stiffness: 1000, damping: 10 });
```
