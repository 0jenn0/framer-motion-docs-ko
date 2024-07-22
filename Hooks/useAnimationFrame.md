# useAnimationFrame

> 제공된 콜백에 최신 프레임 시간을 출력하는 애니메이션 루프입니다.

매 애니메이션 프레임마다 콜백을 한 번씩 실행합니다.

콜백에는 두 개의 인자가 제공됩니다:

- `time`, 콜백이 처음 호출된 이후부터의 총 경과 시간.
- `delta`, 마지막 애니메이션 프레임 이후부터의 총 경과 시간.

```javascript
import { useAnimationFrame } from "framer-motion";

function Component() {
  const ref = useRef(null);

  useAnimationFrame((time, delta) => {
    ref.current.style.transform = `rotateY(${time}deg)`;
  });

  return <div ref={ref} />;
}
```
