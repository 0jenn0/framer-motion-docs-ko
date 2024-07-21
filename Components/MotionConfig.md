# MotionConfig

> Framer Motion에 대한 구성 옵션을 설정합니다.

`MotionConfig` 컴포넌트는 모든 자식 `motion` 컴포넌트에 대한 구성 옵션을 설정하는 데 사용할 수 있습니다. 현재는 `transition` 하나의 prop만 받습니다.

```javascript
import { motion, MotionConfig } from "framer-motion";

export const MyComponent = ({ isVisible }) => (
  <MotionConfig transition={{ duration: 1 }}>
    <motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} />
  </MotionConfig>
);
```

## Props

### transition: Transition

> 전체 트리에 대한 새로운 기본 전환을 정의합니다.

**reducedMotion: "user" | "always" | "never"**

기본값은 `"never"`입니다.

`reducedMotion`은 축소된 모션 처리에 대한 사이트 전체 정책을 설정할 수 있게 해줍니다:

- `user`: 사용자의 기기 설정을 존중합니다.
- `always`: 항상 축소된 모션을 적용합니다.
- `never`: 모션을 축소하지 않습니다.

모션이 축소되면 변형(transform)과 레이아웃 애니메이션이 비활성화됩니다. `opacity`와 `backgroundColor`와 같은 다른 애니메이션은 계속 유지됩니다.

**NOTE**: 비활성화되는 애니메이션 유형과 계속 활성화되는 유형은 피드백을 수집하면서 결정될 것으로 예상됩니다.

**nonce: string**

`nonce` 속성이 있는 콘텐츠 보안 정책([Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/style-src#unsafe_inline_styles))을 사용하는 경우, 동일한 속성을 `MotionConfig`를 통해 전달하면 Motion에 의해 생성된 모든 `style` 블록이 보안 정책을 준수할 수 있습니다.
