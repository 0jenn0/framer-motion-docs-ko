# useTransform

> 하나 또는 여러 개의 다른 모션 값들의 출력을 변환하는 새로운 모션 값을 생성합니다.

`useTransform`은 하나 또는 여러 개의 다른 `MotionValue`들의 출력을 받아 어떤 방식으로든 변경하는 `MotionValue`를 생성합니다.

## Usage

Framer Motion에서 import합니다.

```javascript
import { useTransform } from "framer-motion";
```

### Transform function

`useTransform`은 새로운 값을 반환하는 함수를 받을 수 있습니다.

```javascript
const x = useMotionValue(1);
const y = useMotionValue(1);

const z = useTransform(() => x.get() + y.get()); // 2
```

이 함수 내에서 `get()` 메소드를 통해 읽히는 모든 `MotionValue`들은 자동으로 구독됩니다. 이러한 `MotionValue`들이 변경되면, 새로운 값을 계산하기 위해 다음 프레임에서 함수가 실행됩니다.

```javascript
import { frame } from "framer-motion";

const x = useMotionValue(1);
const y = useMotionValue(1);

const z = useTransform(() => x.get() + y.get()); // 2

x.set(2);

frame.render(() => console.log(z.get())); // 3
```

### Mapping

`useTransform`은 또한 모션 값을 한 범위의 값에서 다른 범위로 매핑할 수 있습니다.

이를 설명하기 위해, 다음과 같은 `x` 모션 값을 살펴보겠습니다:

```javascript
const x = useMotionValue(0);
```

우리는 `useTransform`을 사용하여 `opacity`라는 새로운 모션 값을 만들 수 있습니다. 입력 범위와 출력 범위를 정의함으로써, 우리는 "`x`가 `0`일 때, `opacity`는 `1`이어야 하고, `x`가 `100`일 때, `opacity`는 0이어야 한다"고 말할 수 있습니다.

```javascript
const opacity = useTransform(
  x,
  // Map x from these values:
  [0, 100],
  // Into these values:
  [1, 0]
);
```

이제 `x`가 `50`으로 설정되면, `opacity`는 `0.5`가 될 것입니다.

두 범위는 **항상 같은 길이**여야 합니다.

**입력 범위**는 항상 오름차순이나 내림차순의 선형적인 숫자 시리즈여야 합니다.

**출력 범위**는 모두 같은 타입이어야 하지만, Framer Motion이 지원하는 어떤 타입이든 될 수 있습니다. 예를 들어 숫자, 색상, 그림자 등이 가능합니다.

```javascript
const backgroundColor = useTransform(x, [0, 100], ["#f00", "#00f"]);
```

각 범위에 두 개 이상의 값을 제공하는 것이 가능합니다. 예를 들어, 다음과 같은 매핑으로 `x`가 `0-100` 범위를 벗어날 때마다 `opacity`를 페이드 아웃할 수 있습니다.

```javascript
const opacity = useTransform(x, [-100, 0, 100, 200], [0, 1, 1, 0]);
```

`clamp: false`를 설정함으로써, 범위들은 영구적으로 매핑될 수 있습니다. 예를 들어, 이 예시에서 우리는 "스크롤이 100px 될 때마다 360도 더 회전한다"고 말하고 있습니다:

```javascript
const { scrollY } = useScroll();
const rotate = useTransform(scrollY, [0, 100], [0, 360], { clamp: false });
```

## Options

범위를 전달할 때, 마지막 인수로 옵션 객체를 전달할 수도 있습니다.

```javascript
useTransform(value, input, output, options);
```

**clamp: boolean**
**Default: `true`**

`true`인 경우, 출력을 제공된 범위 내로 제한합니다. `false`인 경우, 입력이 제공된 범위를 벗어나더라도 계속해서 매핑합니다.

```javascript
const y = useTransform(x, [0, 1], [0, 2]);
const z = useTransform(x, [0, 1], [0, 2], { clamp: false });

useEffect(() => {
  x.set(2);
  console.log(y.get()); // 2, input clamped
  console.log(z.get()); // 4
});
```

**ease: EasingFunction | EasingFunction[]**

제공된 범위의 각 값 사이의 혼합을 부드럽게 하는 데 사용할 이징 함수 또는 이징 함수 목록입니다.

이들은 반드시 JavaScript 함수여야 합니다. 모든 명명된 이징([named easing](https://www.framer.com/motion/transition/###ease))은 `"framer-motion"`에서 임포트할 수 있습니다:

```javascript
import { cubicBezier, circOut } from "framer-motion";

// In your component
const y = useTransform(x, [0, 1], [0, 2], { ease: circOut });

const z = useTransform(x, [0, 1], [0, 2], {
  ease: cubicBezier(0.17, 0.67, 0.83, 0.67),
});
```

**mixer: (from: T, to: T) => (p: number) => any**

각 출력 값 세트 사이를 혼합하는 데 사용할 함수입니다.

이는 Framer Motion의 기본 혼합기보다 더 고급 혼합기를 주입하는 데 사용될 수 있습니다. 예를 들어, SVG 경로 변형을 위한 [Flubber](https://github.com/veltman/flubber) 같은 것들입니다.

`from`과 `to`는 각 값 세트가 될 것입니다. 예를 들어, 출력 범위 `["#000", "#f00", "#333"]`가 주어진 경우 두 개의 혼합기가 생성됩니다. 하나는 `"#000"`과 `"#f00"` 사이, 다른 하나는 `"#f00"`과 `"#333"` 사이입니다.

`p`는 `0`과 `1` 사이의 이징된 진행 값으로, `from`과 `to` 사이에서 얼마나 혼합해야 하는지를 정의합니다.
