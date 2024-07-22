# useTime

> 생성된 이후의 지속 시간(밀리초 단위)으로 매 애니메이션 프레임마다 업데이트되는 모션 값입니다.

`useTime`은 모션 값이 처음 생성된 이후의 지속 시간(밀리초 단위)으로 프레임마다 한 번씩 업데이트되는 모션 값을 반환합니다.

이는 특히 영구적으로 애니메이션을 구동하는 데 유용합니다.

## Usage

Framer Motion에서 import합니다.

```javascript
import { useTime } from "framer-motion";
```

`useTime`이 호출되면 새로운 모션 값을 생성합니다. 이 값은 생성된 이후의 시간으로 매 프레임마다 업데이트됩니다.

이를 직접 사용하거나 다른 모션 값 훅들과 조합하여 사용할 수 있습니다.

```javascript
const time = useTime();
const rotate = useTransform(
  time,
  [0, 4000], // For every 4 seconds...
  [0, 360], // ...rotate 360deg
  { clamp: false }
);
```
