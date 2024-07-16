# AnimatePresence

## React 트리에서 컴포넌트가 제거될 때 애니메이션을 적용하세요.

`AnimatePresence`는 컴포넌트가 React 트리에서 제거될 때 애니메이션으로 사라지게 할 수 있습니다.

이는 exit 애니메이션을 가능하게 하기 위해 필요합니다. 왜냐하면 React에는 다음과 같은 기능을 제공하는 라이프사이클 메서드가 없기 때문입니다:

1. 컴포넌트가 언마운트될 예정임을 알려주는 기능
2. 특정 작업(예: 애니메이션)이 완료될 때까지 언마운트를 지연시킬 수 있는 기능

```javascript
import { motion, AnimatePresence } from "framer-motion";

export const MyComponent = ({ isVisible }) => (
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
```

# Usage

## Exit animations

`AnimatePresence`는 **직접적인 자식 요소**들이 React 트리에서 제거될 때 이를 감지하여 작동합니다.

제거되는 자식 요소 내에 포함된 motion 컴포넌트 중 exit prop을 가진 것들은, 전체 트리가 DOM에서 최종적으로 제거되기 전에 해당 애니메이션을 실행합니다.

주의: 직접적인 자식 요소들은 각각 고유한 key prop을 가져야 합니다. 이를 통해 AnimatePresence가 트리 내에서 각 요소의 존재를 추적할 수 있습니다.

```javascript
const MyComponent = ({ isVisible }) => (
  <AnimatePresence>
    {isVisible && (
      <motion.div
        key="modal"
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        exit={{ opacity: 0 }}
      />
    )}
  </AnimatePresence>
);
```

`initial`과 `animate`와 마찬가지로, `exit`도 값들의 `TargetAndTransition` 객체로 정의하거나, 전체 트리를 애니메이션으로 빠져나가게 하는 variant 라벨로 정의할 수 있습니다.

React에서 컴포넌트의 `key`를 변경하면 React는 이를 완전히 새로운 컴포넌트로 취급합니다. 따라서 새 컴포넌트가 마운트되기 전에 이전 컴포넌트가 언마운트됩니다. 그래서 `AnimatePresence`의 단일 자식 컴포넌트의 `key`를 변경함으로써, 우리는 슬라이드쇼와 같은 컴포넌트를 쉽게 만들 수 있습니다

```javascript
export const Slideshow = ({ image }) => (
  <AnimatePresence>
    <motion.img
      key={image.src}
      src={image.src}
      initial={{ x: 300, opacity: 0 }}
      animate={{ x: 0, opacity: 1 }}
      exit={{ x: -300, opacity: 0 }}
    />
  </AnimatePresence>
);
```

## Multiple children

`AnimatePresence`는 여러 자식 컴포넌트에 대해서도 동일한 방식으로 작동합니다. 각 자식 컴포넌트가 고유한 `key`를 가지고 있는지만 확인하면, 컴포넌트들이 트리에 추가되거나 제거될 때 애니메이션이 적용되어 나타나거나 사라집니다.

```javascript
export const Notifications = ({ messages }) => (
  <AnimatePresence>
    {messages.map(({ id, content }) => (
      <motion.li key={id} animate={{ opacity: 1 }} exit={{ opacity: 0 }}>
        {content}
      </motion.li>
    ))}
  </AnimatePresence>
);
```

## Suppressing initial animations

마운트 애니메이션은 이미 `motion` 컴포넌트에서 `initial`과 `animate` prop을 통해 처리됩니다.

만약 motion 컴포넌트에 `initial={false}`가 설정되어 있다면, 이는 animate에 정의된 상태에서 시작할 것입니다. 하지만 때로는, 예를 들어 채팅창이나 슬라이드쇼처럼, 초기 렌더링 이후에 추가된 새로운 컴포넌트에만 애니메이션을 적용하고 싶을 수 있습니다.

`AnimatePresence`에 `initial={false}`를 설정함으로써, `AnimatePresence`가 처음 로드될 때 존재하는 컴포넌트들은 `animate` 상태에서 시작할 것입니다. 이 초기 렌더링 이후에 들어오는 컴포넌트들만 애니메이션과 함께 나타날 것입니다.

```javascript
const MyComponent = ({ isVisible }) => (
  <AnimatePresence initial={false}>
    {isVisible && (
      <motion.div
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        exit={{ opacity: 0 }}
      />
    )}
  </AnimatePresence>
);
```

## Animating custom components

`AnimatePresence`의 자식들은 커스텀 컴포넌트일 수도 있습니다. 유일한 요구사항은 이 컴포넌트 내부 어딘가에 `exit` prop을 가진 최소 하나의 `motion `컴포넌트가 있어야 한다는 것입니다.

주의: DOM에서 제거되는 커스텀 컴포넌트는 여전히 `AnimatePresence`의 **직접적인 자식**이어야만 그 안에 포함된 `exit` 애니메이션(들)이 트리거될 수 있습니다.

```javascript
const Item = () => (
  <div>
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}
    />
  </div>
);

/**
 * The component being removed must be a direct
 * descendant of AnimatePresence due to limitations
 * with React.
 */
export const MyComponent = ({ items }) => (
  <AnimatePresence>
    {items.map(({ id }) => (
      <Item key={id} />
    ))}
  </AnimatePresence>
);
```

# Props

## initial: boolean

`initial={false}`를 전달함으로써, `AnimatePresence`는 컴포넌트가 처음 렌더링될 때 존재하는 자식들에 대한 모든 초기 애니메이션을 비활성화할 것입니다.

```javascript
<AnimatePresence initial={false}>
  {isVisible && (
    <motion.div
      key="modal"
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}
    />
  )}
</AnimatePresence>
```

## custom: any

컴포넌트가 제거될 때, 더 이상 그 props를 업데이트할 기회가 없습니다. 따라서 컴포넌트의 `exit` prop이 동적 variant로 정의되어 있고 새로운 `custom` prop을 전달하고 싶다면, `AnimatePresence`를 통해 이를 수행할 수 있습니다. 이렇게 하면 제거되는 모든 컴포넌트들이 최신 데이터를 사용하여 애니메이션을 수행하도록 보장할 수 있습니다

## mode: "sync" | "wait" | "popLayout"

`AnimatePresence`가 들어오고 나가는 자식 요소들을 어떻게 처리할지 결정합니다.

- `"sync"`: (기본값) 자식 요소들이 추가되거나 제거되는 즉시 애니메이션이 시작됩니다.
- `"wait"`: 새로 들어오는 자식 요소는 나가는 자식 요소의 애니메이션이 완료될 때까지 기다립니다. 주의: 현재는 한 번에 하나의 자식 요소만 렌더링합니다.
- `"popLayout"`: 나가는 자식 요소들이 페이지 레이아웃에서 "튀어나오듯" 제거됩니다. 이를 통해 주변 요소들이 즉시 새로운 레이아웃으로 이동할 수 있습니다.

**커스텀 컴포넌트 주의사항**: `popLayout` 모드를 사용할 때, AnimatePresence의 직접적인 자식인 모든 커스텀 컴포넌트는 반드시 React의 `forwardRef` 함수로 감싸져야 합니다. 이때 제공된 `ref`를 레이아웃에서 튀어나오게 하고 싶은 DOM 노드로 전달해야 합니다.

## onExitComplete(): void

모든 퇴장하는 노드들이 애니메이션을 완료했을 때 발생합니다.

# usePresence

## usePresence(): AlwaysPresent | Present | NotPresent

컴포넌트가 `AnimatePresence`의 자식일 때, `usePresence`를 사용하여 해당 컴포넌트가 React 트리에 여전히 존재하는지에 대한 정보에 접근할 수 있습니다.

만약 `isPresent`가 `false`라면, 이는 컴포넌트가 트리에서 제거되었다는 것을 의미합니다. 하지만 `AnimatePresence`는 `safeToRemove`가 호출될 때까지 실제로 이를 제거하지 않을 것입니다.

```javascript
import { usePresence } from "framer-motion";

export const Component = () => {
  const [isPresent, safeToRemove] = usePresence();

  useEffect(() => {
    !isPresent && setTimeout(safeToRemove, 1000);
  }, [isPresent]);

  return <div />;
};
```

# useIsPresent

## useIsPresent(): boolean

`usePresence`와 유사하지만, `useIsPresent`는 단순히 컴포넌트가 존재하는지 여부만을 반환합니다. 여기에는 `safeToRemove` 함수가 없습니다.

```javascript
import { useIsPresent } from "framer-motion";

export const Component = () => {
  const isPresent = useIsPresent();

  useEffect(() => {
    !isPresent && console.log("I've been removed!");
  }, [isPresent]);

  return <div />;
};
```
