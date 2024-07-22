# useScroll

> `useScroll` 훅을 사용하여 스크롤에 연결된 애니메이션을 만듭니다.

`useScroll`은 진행 표시기나 패럴랙스 효과와 같은 스크롤에 연결된 애니메이션을 만드는 데 사용됩니다.

## Usage

`useScroll`을 Framer Motion에서 import합니다.

```javascript
import { useScroll } from "framer-motion";
```

`useScroll`은 네 가지 모션 값을 반환합니다:

- `scrollX`/`Y`: 절대적인 스크롤 위치를 픽셀 단위로 나타냅니다.
- `scrollXProgress`/`YProgress`: 정의된 오프셋 사이의 스크롤 위치를 0과 1 사이의 값으로 나타냅니다.

## Page scroll

기본적으로, `useScroll`은 페이지 스크롤을 추적합니다.

```javascript
const { scrollY } = useScroll();

useMotionValueEvent(scrollY, "change", (latest) => {
  console.log("Page scroll: ", latest);
});
```

예를 들어, `scrollYProgress`를 진행 바의 `scaleX` 스타일에 직접 전달하여 페이지 스크롤 표시기를 보여줄 수 있습니다.

```javascript
const { scrollYProgress } = useScroll();

return <motion.div style={{ scaleX: scrollYProgress }} />;
```

모션 값으로서, 우리는 `useTransform`과 `useSpring` 같은 다른 모션 값 훅들과 함께 스크롤 정보를 구성할 수 있습니다.

```javascript
const { scrollYProgress } = useScroll();
const scaleX = useSpring(scrollYProgress);

return <motion.div style={{ scaleX }} />;
```

## Element scroll

페이지 대신 스크롤 가능한 요소의 스크롤 위치를 추적하려면, `useScroll`의 `container` 옵션과 해당 요소에 동일한 `ref`를 전달할 수 있습니다.

```javascript
const carouselRef = useRef(null);
const { scrollX } = useScroll({
  container: carouselRef,
});

return <div ref={carouselRef}>{children}</div>;
```

## Element position

요소의 `ref`를 `target` 옵션에 전달함으로써 컨테이너 내에서 해당 요소의 진행 상황을 추적할 수 있습니다.

```javascript
const ref = useRef(null)
const { scrollYProgress } = useScroll({
  target: ref,
  offset: ["start end", "end end"]
})

return  <div ref={ref}>
```

## Scroll offsets

이전 예제에서 우리는 `offset` 옵션도 설정했습니다.

`offset`은 최소 두 개의 교차점을 포함하는 배열입니다.

교차점은 `target`과 `container`가 만나는 지점을 설명합니다. 예를 들어, `"start end"`는 타겟의 시작이 컨테이너의 끝과 만날 때를 의미합니다.

### Accepted values

- 타겟과 컨테이너의 지점은 다음과 같이 정의될 수 있습니다:
  - **Number**: `0`은 축의 시작을, `1`은 끝을 나타냅니다. 따라서 타겟의 상단과 컨테이너의 중간을 정의하려면 `"0 0.5"`로 할 수 있습니다. 이 범위를 벗어난 값도 허용됩니다.
  - **Names**: `"start"`, `"center"`, `"end"`는 각각 `0`, `0.5`, `1`의 명확한 단축어로 사용될 수 있습니다.
  - **Pixels**: `"100px"`, `"-50px"`와 같은 픽셀 값은 타겟/컨테이너의 시작점으로부터 해당 픽셀 수만큼 떨어진 지점으로 정의됩니다.
  - **Percents**: 원시 숫자와 동일하지만 `"0%"`에서 `"100%"`로 표현됩니다.
  - **Viewport**: `"vh"`와 `"vw"` 값이 허용됩니다.

### Shorthand

타겟/컨테이너에 대해 하나의 값만 정의하는 것도 가능합니다.

이름 값은 반복되므로, `"center"`는 `"center center"`와 동등하고 `"end"`는 `"end end"`와 동등합니다.

`"100px"`와 같은 수치 값은 타겟에만 적용되므로, `"100px"`는 타겟의 상단에서 100pixel 지점이 컨테이너의 시작과 만날 때를 정의합니다.

# Options

**container: RefObject<HTMLElement>**

스크롤 위치를 추적할 스크롤 가능한 컨테이너입니다. 기본적으로 이는 윈도우 뷰포트입니다. 그러나 어떤 스크롤 가능한 요소든 될 수 있습니다.

**target: RefObject<HTMLElement>**

기본적으로 이는 컨테이너의 스크롤 가능한 영역입니다. 추가적으로 다른 요소로 설정하여 뷰포트 내에서의 진행 상황을 추적할 수 있습니다.

**axis: "x" | "y"**
**Default: `"y`"**

`offset`을 적용할 스크롤 축입니다.

**offset: [string, string]**
**Default: `["start start", "end end"]`**

스크롤 진행 상황을 결정하는 데 사용할 스크롤 오프셋 목록입니다.
