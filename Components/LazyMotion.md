# LazyMotion

## 번들 크기를 줄이기 위해 Motion의 기능 중 일부를 지연 로드(lazy-loading)할 수 있습니다.

`LazyMotion` 컴포넌트를 사용하면 동기적이나 비동기적으로 `motion` 컴포넌트의 일부 또는 전체 기능을 로드하여 번들 크기를 줄일 수 있습니다.

```javascript
import { LazyMotion, domAnimation, m } from "framer-motion";

export const MyComponent = ({ isVisible }) => (
  <LazyMotion features={domAnimation}>
    <m.div animate={{ opacity: 1 }} />
  </LazyMotion>
);
```

기본적으로, `motion` 컴포넌트는 모든 기능이 번들로 포함되어 있습니다. `m` 컴포넌트는 `motion`과 완전히 동일한 방식으로 사용할 수 있지만, 미리 로드된 기능이 없습니다. 이러한 기능은 `LazyMotion`에 의해 제공됩니다.
이를 통해 초기 번들 크기를 5kb 미만으로 줄일 수 있습니다.

전체 사용 방법은 "Reduce bundle size" 가이드를 참고하세요.

## Props

### features: FeatureBundle | LazyFeatureBundle

`LazyMotion`은 기능 번들을 동기적으로 또는 비동기적으로 제공할 수 있습니다.

```javascript
// features.js
import { domAnimations } from "framer-motion";
export default domAnimations;

// index.js
import { LazyMotion, m } from "framer-motion";

const loadFeatures = import("./features.js").then((res) => res.default);

function Component() {
  return (
    <LazyMotion features={loadFeatures}>
      <m.div animate={{ scale: 1.5 }} />
    </LazyMotion>
  );
}
```

### strict: boolean

strict 값이 true이면 `LazyMotion` 내에서 `motion` 컴포넌트가 렌더링되면 오류를 발생시킵니다.

```javascript
// 이 컴포넌트는 motion 컴포넌트 대신 m 컴포넌트를 사용하지 않으면 코드 분할의 이점이 깨진다는 것을 설명하는 오류를 발생시킬 것입니다.

function Component() {
  return (
    <LazyMotion features={domAnimation} strict>
      <motion.div />
    </LazyMotion>
  );
}
```
