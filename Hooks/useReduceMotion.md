# useReducedMotion

> 사용자가 감소된 모션을 선호하는지 판단하는 훅입니다.

현재 기기에서 '감소된 모션' 설정이 활성화되어 있으면 `true`를 반환하는 훅입니다.

이는 감소된 모션을 기반으로 UI를 변경하는 데 사용될 수 있습니다. 예를 들어, 잠재적으로 멀미를 유발할 수 있는 `x`/`y` 애니메이션을 `opacity`로 대체하거나, 배경 비디오의 자동 재생을 비활성화하거나, 패럴랙스 모션을 끄는 등의 용도로 사용할 수 있습니다.

이 훅은 설정 변경에 적극적으로 반응하여 최신 설정으로 컴포넌트를 다시 렌더링합니다.

```javascript
export function Sidebar({ isOpen }) {
  const shouldReduceMotion = useReducedMotion();
  const closedX = shouldReduceMotion ? 0 : "-100%";

  return (
    <motion.div
      animate={{
        opacity: isOpen ? 1 : 0,
        x: isOpen ? 0 : closedX,
      }}
    />
  );
}
```
