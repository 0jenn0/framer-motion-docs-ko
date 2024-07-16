# Motion components

## Motion 컴포넌트는 60fps 애니메이션과 제스처에 최적화된 DOM 요소입니다.

모든 HTML과 SVG 요소에 대해 motion 컴포넌트가 있습니다. 예를 들어 motion.div, motion.circle 등이 있습니다.

이들은 정적 요소와 정확히 같은 방식으로 작동하지만, 다음과 같은 기능을 제공하는 props를 제공합니다:

- 애니메이션
- 드래그, 팬, 호버, 탭 제스처 추가
- 제스처와 뷰포트에 진입하는 요소에 대해 애니메이션으로 반응
- variants를 통해 React 트리 전체에 걸쳐 깊이 있는 애니메이션

그 외 더 많은 기능이 있습니다.

## Supported values

Motion은 다음을 애니메이션화할 수 있습니다:

- 숫자: `0`, `10` 등
- 숫자를 포함한 문자열: "0vh", "10px" 등
- 색상: Hex, RGB, HSLA
- 여러 숫자 및/또는 색상을 포함하는 복잡한 문자열 (예: "10px 0 #000")

`"block"`과 같은 애니메이션화할 수 없는 값으로 애니메이션을 적용할 때, 이 값은 즉시 설정됩니다. `transitionEnd` 내에서 이 값을 설정하면, 애니메이션이 끝날 때 이 값이 설정됩니다.

```javascript
<motion.div
  animate={{
    x: 0,
    backgroundColor: "#000",
    boxShadow: "10px 10px 0 rgba(0, 0, 0, 0.2)",
    position: "fixed",
    transitionEnd: {
      display: "none",
    },
  }}
/>
```

## Value type conversion

일반적으로 값은 같은 유형(예: 두 개의 px, 두 개의 % 등) 사이에서만 애니메이션화될 수 있습니다.

그러나 HTML 컴포넌트의 `x`, `y`, `width`, `height`, `top`, `left`, `right`, `bottom` 값들은 향상된 지원을 받아 다른 값 유형 사이에서 자유롭게 애니메이션화될 수 있습니다.

```javascript
<motion.div initial={{ x: "100%" }} animate={{ x: "calc(100vw - 50%)" }} />
```

추가로, 모든 색상 유형(hex, HSL, RGB)은 서로 간에 애니메이션화될 수 있습니다.

## Transform

Transform 속성들은 GPU에 의해 가속되어 부드럽게 애니메이션화됩니다. 이들은 개별적으로 설정되고 애니메이션화될 수 있습니다:

- Translate 단축키: x, y, z
- Translate: translateX, translateY, translateZ
- Scale: scale, scaleX, scaleY
- Rotate: rotate, rotateX, rotateY, rotateZ
- Skew: skew, skewX, skewY
- Perspective: transformPerspective

`motion` 컴포넌트는 향상된 style props를 가지고 있어, 여기서도 개별적으로 설정할 수 있습니다.

```javascript
<motion.a
  whileHover={{ scale: 1.2 }}
  whileTap={{ scale: 0.8 }}
  style={{ x: 100 }}
/>
```

편의를 위해 transform 값들은 특정 순서로 적용됩니다: translate, scale, rotate, skew.
그러나 `transformTemplate` prop을 사용하여 이 기본 순서를 커스터마이즈할 수 있습니다.

```javascript
function template({ rotate, x }) {
  return `rotate(${rotate}) translateX(${x})`;
}

return (
  <motion.div
    transformTemplate={template}
    animate={{ rotate: 360 }}
    style={{ rotate: 0, x: "calc(50vh - 100px)" }}
  />
);
```

**SVG 참고**: SVG 컴포넌트의 경우, `x`와 `y` 속성(`transform` 스타일과 대조적으로)은 애니메이션 props 내에서 `attrX`와 `attrY`를 사용하여 설정할 수 있습니다.

## Transform origin

`transform-origin`은 개별적으로 설정하고 애니메이션화할 수 있는 세 가지 단축값을 가집니다:

- `originX`
- `originY`
- `originZ`

숫자로 설정되면, `originX`와 `Y`는 기본적으로 `0`과 `1` 사이의 진행 값입니다. `originZ`는 기본적으로 픽셀 단위입니다.

```javascript
<motion.div style={{ originX: 0.5 }} />
```

### CSS 변수

Motion은 CSS 변수의 값을 애니메이션화할 수 있으며, 애니메이션 대상으로 CSS 변수를 읽을 수도 있습니다.

### 애니메이션에서 미리 정의된 CSS 변수 사용하기

HTML motion 컴포넌트는 해당 변수가 컴포넌트 조상에 정의되어 있는 한, CSS 변수로부터 그리고 CSS 변수로 애니메이션화할 수 있습니다.

```javascript
<motion.li animate={{ background: "var(--action)" }} />
```

### CSS 변수 애니메이션화하기

CSS 변수를 정의하고 애니메이션화함으로써, 부모 motion 컴포넌트를 사용하여 여러 DOM 자식들을 선언적으로 애니메이션화할 수 있습니다.
TypeScript에서 CSS 변수를 애니메이션화할 때, 타입 오류를 방지하기 위해 prop을 any로 캐스팅해야 합니다 (변수 이름의 수가 무한하기 때문입니다).

CSS 변수는 또한 임의의 타입이므로, Motion은 그들의 기본 타입을 추론할 수 없습니다. Motion이 rotate를 deg로 설정해야 한다는 것을 이해하기 때문에 rotate를 숫자로 애니메이션화할 수 있지만, '--rotate'는 명시적으로 단위 타입과 함께 설정되어야 합니다 (예: '360deg').

```javascript
<motion.ul
  initial={{ '--rotate': '0deg' } as any}
  animate={{ '--rotate': '360deg' } as any}
  transition={{ duration: 2, repeat: Infinity }}
>
  <li style={{ transform: 'rotate(var(--rotate))' }} />
  <li style={{ transform: 'rotate(var(--rotate))' }} />
  <li style={{ transform: 'rotate(var(--rotate))' }} />
</motion.ul>
```

이 코드는 CSS 변수 '--rotate'를 애니메이션화하여 모든 자식 li 요소를 동시에 회전시킵니다.

### SVG 선 그리기

많은 SVG 요소에서 세 가지 특별한 속성을 사용하여 선 그리기 애니메이션을 만들 수 있습니다: `pathLength`, `pathSpacing`, 그리고 `pathOffset`입니다.

이들은 모두 `0`과 `1` 사이의 값으로 설정되며, 여기서 `1`은 경로의 측정된 길이를 나타냅니다.
경로 애니메이션은 `circle`, `ellipse`, `line`, `path`, `polygon`, `polyline`, 그리고 `rect` 요소와 호환됩니다.
이 기능을 사용하면 SVG 요소의 윤곽선이 그려지는 애니메이션을 쉽게 만들 수 있습니다. 예를 들어, `pathLength`를 `0`에서 `1`로 애니메이션화하면 선이 점진적으로 그려지는 효과를 만들 수 있습니다.

## Custom components

어떤 컴포넌트든 `motion()` 함수로 감싸서 `motion` 컴포넌트로 만들 수 있습니다.
제공된 컴포넌트는 애니메이션을 적용하고자 하는 DOM 요소에 ref를 전달해야 합니다.
또한, `motion()`은 React 렌더 함수 내에서 호출되어서는 안 됩니다.

```javascript
const Component = React.forwardRef((props, ref) => <div ref={ref} />);
const MotionComponent = motion(Component);
```

motion에 문자열을 전달하여 커스텀 DOM 요소를 생성하는 것도 가능합니다.

```javascript
motion(Component, { forwardMotionProps: true });
```

## Performance

Motion은 성능 향상을 위해 React 렌더 사이클 밖에서 값을 애니메이션화합니다.

상태 대신 MotionValues를 사용하여 시각적 속성을 업데이트하면 리렌더링을 방지할 수 있습니다.

가능한 경우, GPU 가속이 적용되는 transform 값과 opacity만 애니메이션화하세요. 이렇게 하면 현대적인 모바일 기기에서 수백 개의 레이어를 애니메이션화할 수 있습니다.

```javascript
// GPU accelerated (fast)
<motion.div style={{ x: 0 }} animate={{ x: 100 }} />

// CPU drawing (slower)
<motion.div style={{ left: 0 }} animate={{ left: 100 }} />
```

## Server-side rendering

`motion` 컴포넌트는 서버 사이드 렌더링과 완전히 호환됩니다. 이는 컴포넌트의 초기 상태가 서버에서 생성된 출력에 반영된다는 의미입니다.

```javascript
// 서버는 `translateX(100px)`를 출력합니다
<motion.div initial={false} animate={{ x: 100 }} />
```

### Exceptions

다음 SVG 값들은 현재 서버 사이드 렌더링과 호환되지 않습니다: `scale`, `rotate`, `pathLength`, `pathOffset`, `pathSpacing`.
`scale`과 `rotate`는 `transformOrigin`의 동적 계산에 의존합니다 - 서버 사이드에서 이를 지원하려면 `originX`와 `originY`를 문자열(px 또는 %)로 설정해야 합니다.

```javascript
<motion.circle style={{ scale: 2, originX: "100px", originY: "100px" }} />
```

`path` 값들은 전체 경로 길이의 측정에 의존합니다. `strokeDasharray`를 `"0 1"`로 설정하면 Motion이 이를 측정할 수 있을 때까지 경로를 숨깁니다.

```javascript
<motion.path strokeDasharray="0 1" />
```

이렇게 하면 초기 렌더링 시 경로가 보이지 않고, 클라이언트 사이드에서 JavaScript가 로드된 후에 애니메이션이 시작됩니다.

## Props

**Note:** 추가 props는 제스처와 스크롤 애니메이션 페이지에서 찾을 수 있습니다.

### Animation

**#**initial: boolean | Target | VariantLabels

시작 시 적용할 속성, 변형 라벨 또는 변형 라벨 배열입니다.
false로 설정하면 animate의 값으로 초기화됩니다 (마운트 애니메이션 비활성화).

```javascript
// 값으로 설정
<motion.div initial={{ opacity: 1 }} />
// 변형으로 설정
<motion.div initial="visible" variants={variants} />
// 여러 변형
<motion.div initial={["visible", "active"]} variants={variants} />
// false로 설정 (마운트 애니메이션 비활성화)
<motion.div initial={false} animate={{ opacity: 0 }} />

```

**animate**: AnimationControls | TargetAndTransition | VariantLabels | boolean

애니메이션할 값, 변형 라벨(들), 또는 AnimationControls입니다.

```javascript
// 값으로 설정
<motion.div animate={{ opacity: 1 }} />
// 변형으로 설정
<motion.div animate="visible" variants={variants} />
// 여러 변형
<motion.div animate={["visible", "active"]} variants={variants} />
// AnimationControls
<motion.div animate={animation} />
```

**exit**: TargetAndTransition | VariantLabels

이 컴포넌트가 트리에서 제거될 때 애니메이션할 대상입니다.
이 컴포넌트는 이 종료 애니메이션을 활성화하기 위해 AnimatePresence의 첫 번째 애니메이션 가능한 자식이어야 합니다.
이 제한은 React가 애니메이션이 완료될 때까지 컴포넌트의 언마운팅을 지연시키는 것을 허용하지 않기 때문에 존재합니다. 이 제한이 해결되면 AnimatePresence 컴포넌트는 불필요해질 것입니다.

```javascript
import { AnimatePresence, motion } from "framer-motion";

export const MyComponent = ({ isVisible }) => {
  return (
    <AnimatePresence>
      {isVisible && (
        <motion.div
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
        />
      )}
    </AnimatePresence>
  );
};
```

### transition: Transition

기본 전환입니다. animate에 transition이 정의되지 않은 경우, 여기서 정의된 전환을 사용합니다.

```javascript
const spring = {
  type: "spring",
  damping: 10,
  stiffness: 100
}
<motion.div transition={spring} animate={{ scale: 1.2 }} />
```

### variants: Variants

variants를 사용하면 애니메이션 상태를 정의하고 이름으로 구성할 수 있습니다. 이를 통해 단일 animate prop을 전환하여 컴포넌트 트리 전체의 애니메이션을 제어할 수 있습니다.

delayChildren과 staggerChildren 같은 transition 옵션을 사용하여 자식 애니메이션이 부모에 대해 상대적으로 언제 재생되는지 조정할 수 있습니다.

하나 이상의 motion 컴포넌트의 variants prop에 variants를 전달한 후, 이 variants는 animate, initial, whileFocus, whileTap, whileHover prop의 값 대신 사용될 수 있습니다.

```javascript
const variants = {
  active: {
    backgroundColor: "#f00"
  },
  inactive: {
    backgroundColor: "#fff",
    transition: { duration: 2 }
  }
}
<motion.div variants={variants} animate="active" />
```

### style: MotionStyle

React DOM의 style prop으로, MotionValue와 별도의 transform 값을 지원하도록 향상되었습니다.

```javascript
export const MyComponent = () => {
  const x = useMotionValue(0);
  return <motion.div style={{ x, opacity: 1, scale: 0.5 }} />;
};
```

### variants: Variants

variants를 사용하면 애니메이션 상태를 정의하고 이름으로 구성할 수 있습니다. 이를 통해 단일 animate prop을 전환하여 컴포넌트 트리 전체의 애니메이션을 제어할 수 있습니다.

delayChildren과 staggerChildren 같은 transition 옵션을 사용하여 자식 애니메이션이 부모에 대해 상대적으로 언제 재생되는지 조정할 수 있습니다.

하나 이상의 motion 컴포넌트의 variants prop에 variants를 전달한 후, 이 variants는 animate, initial, whileFocus, whileTap, whileHover prop의 값 대신 사용될 수 있습니다.

```javascript
const variants = {
  active: {
    backgroundColor: "#f00"
  },
  inactive: {
    backgroundColor: "#fff",
    transition: { duration: 2 }
  }
}
<motion.div variants={variants} animate="active" />
```

### style: MotionStyle

React DOM의 style prop으로, MotionValue와 별도의 transform 값을 지원하도록 향상되었습니다.

```javascript
export const MyComponent = () => {
  const x = useMotionValue(0);
  return <motion.div style={{ x, opacity: 1, scale: 0.5 }} />;
};
```

## Layout animation

### layout: boolean | "position" | "size"

`true`로 설정하면, 이 컴포넌트는 레이아웃이 변경될 때 자동으로 새 위치로 애니메이션됩니다.
이는 성능이 좋은 transform을 사용하여 레이아웃 애니메이션을 수행합니다. 이 기술의 일부는 요소의 scale을 애니메이션화하는 것을 포함합니다. 이로 인해 자식 요소, `boxShadow`, `borderRadius`에 시각적 왜곡이 생길 수 있습니다.
직접적인 자식 요소의 왜곡을 수정하려면, 그 요소에도 layout을 추가하세요.
boxShadow와 borderRadius는 이 컴포넌트에서 이미 애니메이션되고 있다면 자동으로 수정됩니다. 그렇지 않다면, initial prop을 통해 직접 설정하세요.
layout이 "position"으로 설정되면, 위치만 애니메이션됩니다. 이는 늘어날 때 좋아 보이지 않는 텍스트 컴포넌트에 좋습니다.
layout이 "size"로 설정되면, 크기만 애니메이션됩니다.

### layoutId: string

layoutId를 가진 컴포넌트가 React 트리에서 제거된 후 다른 곳에 추가되면, 이전 컴포넌트의 경계 상자와 최신 애니메이션 값에서 시각적으로 애니메이션됩니다.

이전 컴포넌트가 트리에 남아있으면 새 컴포넌트로 크로스페이드됩니다.

```javascript
{
  items.map((item) => (
    <motion.li layout>
      {item.name}
      {item.isSelected && <motion.div layoutId="underline" />}
    </motion.li>
  ));
}
```

### layoutDependency: any

기본적으로 레이아웃 변경은 매 렌더링마다 감지됩니다. 성능을 향상시키기 위해, layoutDependency에 값을 전달하여 레이아웃 변경이 발생할 가능성이 있는 시기를 힌트할 수 있습니다.
그러면 motion 컴포넌트는 전달된 값이 변경되거나 동일한 LayoutGroup 내의 다른 motion 컴포넌트의 layoutDependency가 변경될 때만 자신을 측정합니다.

### layoutScroll: boolean

스크롤 가능한 요소 내에서 레이아웃 애니메이션이 올바르게 작동하려면 스크롤 오프셋을 측정해야 합니다. 성능상의 이유로, Framer Motion은 모든 조상 요소의 스크롤 오프셋을 측정하지 않습니다. 측정해야 하는 요소에 layoutScroll prop을 추가하세요.

```javascript
<motion.div layoutScroll style={{ overflow: "scroll" }}>
  <motion.div layout />
</motion.div>
```

### onLayoutAnimationStart(): void

이 컴포넌트에서 레이아웃 애니메이션이 시작될 때 실행될 콜백입니다.

### onLayoutAnimationComplete(): void

이 컴포넌트에서 레이아웃 애니메이션이 완료될 때 실행될 콜백입니다.

## Animation events
