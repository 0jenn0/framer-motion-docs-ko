# Scroll animations

## Framer Motion에서 스크롤 연결 및 스크롤 트리거 애니메이션을 만드는 방법

주로 두 가지 유형의 스크롤 애니메이션이 있으며, 둘 다 Framer Motion으로 구현할 수 있습니다.

스크롤 연결 애니메이션은 애니메이션의 진행이 스크롤 진행도에 직접 연결되는 경우입니다. 스크롤 트리거 애니메이션은 요소가 뷰포트에 들어오거나 나갈 때 일반 애니메이션이 트리거되는 경우입니다.

## Scroll-linked animations

스크롤 연결 애니메이션은 모션 값과 `useScroll` 훅을 사용하여 만듭니다.

```javascript
import { motion, useScroll } from "framer-motion";

function Component() {
  const { scrollYProgress } = useScroll();

  return <motion.div style={{ scaleX: scrollYProgress }} />;
}
```

[![Edit framer-motion-2-layout-animations](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/framer-motion-usescroll-xwdxbt?from-embed)

요소 스크롤 추적, 뷰포트 내 요소 위치 파악, 패럴랙스 효과 생성 등에 대해 알아보려면 `useScroll` 문서 전체를 읽어보세요.

## No-code

가장 쉬운 API는 API가 없는 것입니다. 이것이 바로 Framer Motion이 창의적인 전문가를 위한 웹 빌더인 Framer에서 스크롤 연결 애니메이션을 만드는 데 사용되는 이유입니다.
스크롤 변환(Scroll Transforms) 기능을 사용하면 코드 없이 몇 초 만에 애니메이션을 만들 수 있습니다.

## Scroll-triggered animations

스크롤 트리거 애니메이션은 요소가 뷰포트에 들어오거나 나갈 때 시작되는 일반적인 애니메이션입니다.

whileInView prop을 사용하여 요소가 뷰포트에 있을 때 애니메이션할 속성 집합과 선택적으로 전환(transition)을 정의하여 스크롤 트리거 애니메이션을 만들 수 있습니다.

```javascript
<motion.div initial={{ opacity: 0 }} whileInView={{ opacity: 1 }} />
```

## Props

### whileInView: VariantLabels | TargetAndTransition

요소가 뷰포트에 있을 때 애니메이션할 속성이나 variant 라벨입니다.

```javascript
<motion.div whileInView={{ opacity: 1 }} />
```

### viewport: ViewportOptions

뷰포트가 어떻게 감지되는지 정의하는 뷰포트 옵션 객체입니다.

```javascript
<motion.div initial="hidden" whileInView="visible" viewport={{ once: true }} />
```

### onViewportEnter(entry): void

요소가 뷰포트에 들어올 때 트리거되는 콜백입니다. 교차 이벤트의 세부 정보가 있는 IntersectionObserverEntry를 제공하거나, 브라우저가 IntersectionObserver를 지원하지 않는 경우 null을 제공합니다.

### onViewportLeave(entry): void

요소가 뷰포트를 떠날 때 트리거되는 콜백입니다. 교차 이벤트의 세부 정보가 있는 IntersectionObserverEntry를 제공합니다. 브라우저가 IntersectionObserver를 지원하지 않는 경우에는 절대 호출되지 않습니다.

## Viewport options

### once: boolean

만약 true라면, 요소가 뷰포트에 한 번 들어오면 `whileInView` 상태를 유지합니다. 이후에는 더 이상 뷰포트 콜백이 트리거되지 않습니다.

```javascript
<motion.div initial="hidden" whileInView="visible" viewport={{ once: true }} />
```

### root: RefObject<Element>

기본적으로 요소는 윈도우 뷰포트에 들어올 때 뷰포트 내에 있다고 간주됩니다.
조상 요소와 viewport.root에 ref를 전달하여 해당 조상 요소를 측정된 뷰포트로 사용할 수 있습니다.

```javascript
function Component() {
  const scrollRef = useRef(null);
  return (
    <div ref={scrollRef} style={{ overflow: "scroll" }}>
      <motion.div
        initial={{ opacity: 0 }}
        whileInView={{ opacity: 1 }}
        viewport={{ root: scrollRef }}
      />
    </div>
  );
}
```

### margin: string

요소가 뷰포트에 들어왔는지 감지할 때 뷰포트에 추가할 마진입니다.
기본값은 "0px"입니다. 단일 값을 사용하여 모든 면에 마진을 추가할 수 있습니다(예: "200px"). 또는 여러 값을 정의하여 각 축에 마진을 할당할 수 있습니다(상/우/하/좌 순서, 예: "0px -20px 0px 100px").

### amount: "some" | "all" | number

기본값은 "some"이며, 이 옵션은 요소가 뷰포트 내에 있다고 간주되기 위해 뷰포트와 교차해야 하는 요소의 양을 정의합니다.
이 설명은 Framer Motion의 viewport prop에 대한 추가 옵션들을 자세히 설명하고 있습니다. once, root, margin, amount 등의 옵션을 통해 뷰포트 감지 방식을 세밀하게 제어할 수 있습니다.
