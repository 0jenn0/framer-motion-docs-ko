# useAnimate

> 스코프가 지정된 선택자와 자동 정리 기능을 갖춘 animate 함수를 생성합니다.

`useAnimate`는 컴포넌트 내의 요소들에 대해 스코프가 지정된 `animate` 함수를 사용할 수 있는 방법을 제공합니다.

이는 `scope` ref와 모든 DOM 선택자가 이 ref에 스코프가 지정된 `animate` 함수를 제공합니다.

```javascript
function Component() {
  const [scope, animate] = useAnimate();

  useEffect(() => {
    // 이 "li" 선택자는 scope를 받는 요소의 자식들만 선택할 것입니다.
    animate("li", { opacity: 1 });
  });

  return <ul ref={scope}>{children}</ul>;
}
```

추가로, `useAnimate`를 호출하는 컴포넌트가 제거될 때, 그 반환된 `animate` 함수로 시작된 모든 애니메이션들이 자동으로 정리될 것입니다.

### `useAnimate()` vs `<motion.div />`

`motion components`를 통한 선언적 애니메이션은 작성하고 유지보수하기가 더 간단한 경향이 있습니다. 하지만, `useAnimate()`를 통한 명령형 애니메이션은 매우 강력하여 다음과 같은 것들을 가능하게 합니다:

- 복잡한 애니메이션 시퀀싱
- 재생 제어 (`pause`/`play` 등)
- `MotionValue`를 직접 애니메이션화
- DOM 선택자를 통해 렌더링된 컴포넌트에 직접 접근할 수 없는 "블랙박스" 컴포넌트의 애니메이션화

결국 어떤 것을 사용할지는 여러분의 필요와 사용 사례에 따라 다릅니다.

## Usage

`"framer-motion`에서 import합니다.

```javascript
import { useAnimate } from "framer-motion";
```

`useAnimate`는 두 개의 인자를 반환합니다. `scope` ref와 `animate` 함수입니다.

```javascript
function Component() {
  const [scope, animate] = useAnimate()
```

이 `scope` ref는 일반 HTML/SVG 요소나 `motion` 컴포넌트에 전달되어야 합니다.

```javascript
function Component({ children }) {
  const [scope, animate] = useAnimate();

  return <ul ref={scope}>{children}</ul>;
}
```

이렇게 스코프가 지정된 `animate` 함수는 이제 효과(effects)와 이벤트 핸들러에서 요소들을 애니메이션화하는 데 사용될 수 있습니다.

우리는 스코프가 지정된 요소를 직접 사용할 수 있습니다:

```javascript
animate(scope.current, { opacity: 1 }, { duration: 1 });
```

또는 선택자를 전달함으로써 사용할 수 있습니다.

```javascript
animate("li", { backgroundColor: "#000" }, { ease: "linear" });
```

이 선택자는 `"li"`입니다. 하지만 우리는 페이지의 모든 `li` 요소를 선택하는 것이 아니라, 스코프가 지정된 요소의 자식인 것들만 선택하고 있습니다.

### Scroll-triggered animations

애니메이션은 `useAnimate`를 `useInView`와 함께 사용하여 스코프가 화면에 스크롤되어 보일 때 트리거될 수 있습니다

```javascript
import { useAnimate, useInView } from "framer-motion";

function Component() {
  const [scope, animate] = useAnimate();
  const isInView = useInView(scope);

  useEffect(() => {
    if (isInView) {
      animate(scope.current, { opacity: 1 });
    }
  }, [isInView]);

  return (
    <ul ref={scope}>
      <li />
      <li />
      <li />
    </ul>
  );
}
```

### Exit animations

컴포넌트가 제거될 때 `useAnimate`를 `usePresence`와 함께 사용하여 자신만의 퇴장 애니메이션을 구성하는 것이 가능합니다.

```javascript
import { useAnimate, usePresence } from "framer-motion";

function Component() {
  const [isPresent, safeToRemove] = usePresence();
  const [scope, animate] = useAnimate();

  useEffect(() => {
    if (isPresent) {
      const enterAnimation = async () => {
        await animate(scope.current, { opacity: 1 });
        await animate("li", { opacity: 1, x: 0 });
      };
      enterAnimation();
    } else {
      const exitAnimation = async () => {
        await animate("li", { opacity: 0, x: -100 });
        await animate(scope.current, { opacity: 0 });
        safeToRemove();
      };

      exitAnimation();
    }
  }, [isPresent]);

  return (
    <ul ref={scope}>
      <li />
      <li />
      <li />
    </ul>
  );
}
```

이제 이 컴포넌트는 `AnimatePresence`의 자식으로 조건부 렌더링될 수 있습니다.

```javascript
<AnimatePresence>{show ? <Component key="dialog" /> : null}</AnimatePresence>
```
