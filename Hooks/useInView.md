# useInView

> 요소가 뷰포트 내에 있을 때를 위한 간단한 상태 훅입니다.

`useInView`는 제공된 요소가 뷰포트 내에 있는지를 감지하는 아주 작은 (0.6kb) 훅입니다.

```javascript
function Component() {
  const ref = useRef(null);
  const isInView = useInView(ref);

  return <div ref={ref} />;
}
```

## Usage

`useInView`를 framer-motion에서 import합니다.

```javascript
import { useInView } from "framer-motion";
```

`useInView`는 모든 HTML 요소의 가시성을 추적할 수 있습니다. `ref` 객체를 `useInView`와 HTML 요소 모두에 전달하세요.

```javascript
function Component() {
  const ref = useRef(null);
  const isInView = useInView(ref);

  return <div ref={ref} />;
}
```

요소가 뷰포트 밖에 있는 동안 `useInView`는 `false`를 반환합니다. 요소가 뷰 안으로 이동하면, 컴포넌트를 다시 렌더링하고 `true`를 반환합니다.

## Effects

`useInView`는 순수한 React 상태이므로, isInView가 변경될 때 함수를 실행하는 것은 이를 `useEffect`에 전달하는 문제입니다.

```javascript
useEffect(() => {
  console.log("Element is in view: ", isInView);
}, [isInView]);
```

## Options

**root: RefObject<HTMLElement>**

기본적으로 `useInView`는 요소가 윈도우 뷰포트에 들어오고 나가는 것을 추적합니다. `root`를 스크롤 가능한 부모 요소의 ref로 설정하면, 해당 요소를 대신 뷰포트로 사용합니다.

```javascript
function Carousel() {
  const container = useRef(null);
  const ref = useRef(null);
  const isInView = useInView({ root: container });

  return (
    <div ref={container} style={{ overflow: "scroll" }}>
      <div ref={ref} />
    </div>
  );
}
```

**margin: string**

`root` 요소 또는 윈도우 뷰포트에 적용할 하나 이상의 마진입니다. 이를 통해 요소가 뷰포트 내부에 있다고 간주되는 지점을 확장하거나 축소할 수 있습니다.
마진은 `px` 또는 `%`로 정의되어야 합니다.

```javascript
const isInView = useInView({
  margin: "0px 100px -50px 0px",
});
```

**NOTE**: 브라우저 보안상의 이유로, root가 명시적으로 정의되지 않은 경우 [크로스 오리진 iframe 내에서는 `margin`이 적용되지 않습니다.](https://w3c.github.io/IntersectionObserver/#dom-intersectionobserver-rootmargin)

**once: boolean**

`once: true`로 설정하면, 요소가 한 번 뷰 안에 들어온 후에는 `useInView`가 요소 관찰을 중단하고 항상 `true`를 반환합니다.

```javascript
const isInView = useInView(ref, { once: true });
```

**amount: "some" | "all" | number**

`useInView`가 `true`를 반환하기 위해 뷰포트에 들어와야 하는 요소의 양입니다.

이는 `0`에서 `1` 사이의 숫자로 정의되며, `0`은 요소의 일부 또는 어느 부분이든, `1`은 요소의 전체를 의미합니다.
