# Transition

transition은 값들이 한 상태에서 다른 상태로 어떻게 애니메이션될지를 정의합니다.
transition은 두 값 사이를 애니메이션할 때 사용할 애니메이션 유형을 정의합니다.

```javascript
<motion.div animate={{ x: 100 }} transition={{ delay: 1 }} />
```

또한 Tween, Spring 또는 Inertia 중 어떤 유형의 애니메이션을 사용할지 정의하는 props를 받을 수 있습니다.

```javascript
<motion.div
  animate={{ x: 100 }}
  transition={{ type: "spring", stiffness: 100 }}
/>
```

## Value-specific transitions

각 특정 값에 대해 다른 transition 설정을 정의할 수 있습니다.

```javascript
transition={{
  ease: "linear",
  duration: 2,
  x: { duration: 1 }
}}
```

## Orchestration

### delay: number

이 시간(초 단위)만큼 애니메이션을 지연시킵니다. 기본값은 0입니다.

```javascript
const transition = {
  delay: 0.2,
};
```

delay를 음수 값으로 설정하면, 애니메이션이 그만큼 진행된 상태에서 시작합니다. 예를 들어, 1초 진행된 상태에서 시작하려면 delay를 -1로 설정할 수 있습니다.

### delayChildren: number

variants를 사용할 때, 자식 애니메이션이 이 시간(초 단위) 후에 시작됩니다. transition 속성을 Frame과 variant 모두에 직접 추가할 수 있습니다. variant에 추가하면 일반적으로 더 유연성이 높아지며, 시각적 상태별로 지연을 사용자 정의할 수 있습니다.

```javascript
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      delayChildren: 0.5,
    },
  },
};

const item = {
  hidden: { opacity: 0 },
  show: { opacity: 1 },
};

return (
  <motion.ul variants={container} initial="hidden" animate="show">
    <motion.li variants={item} />
    <motion.li variants={item} />
  </motion.ul>
);
```

### staggerChildren: number

variants를 사용할 때, 자식 컴포넌트의 애니메이션을 이 시간(초 단위)만큼 순차적으로 시작할 수 있습니다.
예를 들어, `staggerChildren`이 `0.01`이면, 첫 번째 자식은 `0`초 후에, 두 번째는 `0.01`초 후에, 세 번째는 `0.02`초 후에 시작됩니다.
계산된 순차 지연 시간은 `delayChildren`에 추가됩니다.

```javascript
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      staggerChildren: 0.5,
    },
  },
};
const item = {
  hidden: { opacity: 0 },
  show: { opacity: 1 },
};
return (
  <motion.ol variants={container} initial="hidden" animate="show">
    <motion.li variants={item} />
    <motion.li variants={item} />
  </motion.ol>
);
```

### staggerDirection: number

자식 요소들을 순차적으로 애니메이션화할 방향입니다.
값이 1이면 첫 번째부터 마지막까지, -1이면 마지막부터 첫 번째까지 순차적으로 적용됩니다.

```javascript
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      delayChildren: 0.5,
      staggerDirection: -1,
    },
  },
};

const item = {
  hidden: { opacity: 0 },
  show: { opacity: 1 },
};

return (
  <motion.ul variants={container} initial="hidden" animate="show">
    <motion.li variants={item} size={50} />
    <motion.li variants={item} size={50} />
  </motion.ul>
);
```

### when: false | "beforeChildren" | "afterChildren" | string

트랜지션과 그 자식 요소들 간의 관계를 설명합니다. 기본값은 false입니다.

variants를 사용할 때, 트랜지션은 자식 요소들과의 관계에 따라 예약될 수 있습니다. `"beforeChildren"`은 자식 트랜지션을 시작하기 전에 이 트랜지션을 완료하고, `"afterChildren"`은 자식 트랜지션을 완료한 후 이 트랜지션을 시작합니다.

```javascript
const list = {
  hidden: {
    opacity: 0,
    transition: { when: "afterChildren" },
  },
};

const item = {
  hidden: {
    opacity: 0,
    transition: { duration: 2 },
  },
};

return (
  <motion.ul variants={list} animate="hidden">
    <motion.li variants={item} />
    <motion.li variants={item} />
  </motion.ul>
);
```

### repeat: number

트랜지션을 반복할 횟수입니다. 영구 반복을 위해 Infinity로 설정할 수 있습니다.
repeatType을 설정하지 않으면 애니메이션이 반복됩니다.

```javascript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ repeat: Infinity, duration: 2 }}
/>
```

### repeatType: "loop" | "reverse" | "mirror"

애니메이션을 어떻게 반복할지 지정합니다. 다음 중 하나일 수 있습니다:

- loop: 애니메이션을 처음부터 반복합니다
- reverse: 앞으로와 뒤로의 재생을 번갈아 합니다
- mirror: from과 to를 번갈아 전환합니다

```javascript
<motion.div
  animate={{ rotate: 180 }}
  transition={{
    repeat: 1,
    repeatType: "reverse",
    duration: 2,
  }}
/>
```

### repeatDelay: number

애니메이션을 반복할 때, repeatDelay는 각 반복 사이에 기다릴 시간의 길이를 초 단위로 설정합니다.

```javascript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ repeat: Infinity, repeatDelay: 1 }}
/>
```

## Tween

### type: "tween"

`type`을 `"tween"`으로 설정하면 지속 시간 기반 애니메이션을 사용합니다. `type` 속성 없이 오케스트레이션이 아닌 transition 값이 설정되면, 이것이 기본 애니메이션으로 사용됩니다.

```javascript
<motion.path
  animate={{ pathLength: 1 }}
  transition={{ duration: 2, type: "tween" }}
/>
```

### duration: number

tween 애니메이션의 지속 시간입니다. 기본값은 0.3이며, 키프레임 시리즈를 애니메이션할 경우 0.8입니다.

```javascript
const variants = {
  visible: {
    opacity: 1,
    transition: { duration: 2 },
  },
};
```

### ease: Easing | Easing[]

사용할 이징 함수입니다. 다음 중 하나로 설정할 수 있습니다.

- 기존 이징 함수의 이름
- 4개의 숫자로 구성된 배열로 cubic bezier 곡선을 정의
- 0-1 사이의 값을 받아 반환하는 이징 함수

키프레임 애니메이션을 위해 여러 값의 배열로 애니메이션 값을 설정한 경우, ease를 이징 함수의 배열로 설정하여 각 값 사이에 다른 이징을 적용할 수 있습니다.

```javascript
<motion.div
  animate={{ opacity: 0 }}
  transition={{ ease: [0.17, 0.67, 0.83, 0.67] }}
/>
```

Framer에 내장된 이징 함수의 이름은 다음과 같습니다:

- "linear"
- "easeIn", "easeOut", "easeInOut"
- "circIn", "circOut", "circInOut"
- "backIn", "backOut", "backInOut"
- "anticipate"

### from: number | string

애니메이션의 시작 값입니다. 기본적으로 애니메이션 값의 현재 상태입니다.

```javascript
<motion.div animate={{ rotate: 180 }} transition={{ from: 90, duration: 2 }} />
```

### times: number[]

키프레임을 애니메이션할 때, times를 사용하여 각 키프레임에 도달하는 애니메이션의 시점을 결정할 수 있습니다. times의 각 값은 duration을 나타내는 0에서 1 사이의 값입니다.
키프레임 수와 times의 수가 같아야 합니다. 기본값은 균등하게 분배된 지속 시간의 배열입니다.

```javascript
<motion.div
  animate={{ scale: [0, 1, 0.5, 1] }}
  transition={{ times: [0, 0.1, 0.9, 1] }}
/>
```

## Spring

### type: "spring"

type을 "spring"으로 설정하면 자연스러운 움직임을 위해 스프링 물리학을 사용하여 애니메이션을 만듭니다. 기본적으로 타입은 "spring"으로 설정됩니다.

```javascript
<motion.div animate={{ rotate: 180 }} transition={{ type: "spring" }} />
```

### duration: number

애니메이션의 지속 시간을 초 단위로 정의합니다. 스프링 애니메이션은 최대 10초까지 가능합니다.

bounce가 설정되면 기본값은 0.8입니다.

Note: stiffness, damping 또는 mass가 설정되면 duration과 bounce는 무시됩니다.

```javascript
<motion.div
  animate={{ x: 100 }}
  transition={{ type: "spring", duration: 0.8 }}
/>
```

### bounce: number

`bounce`는 스프링 애니메이션의 "탄력성"을 결정합니다.
`0`은 탄력 없음, `1`은 매우 탄력적입니다.
duration이 설정되면 기본값은 `0.25`입니다.

Note: stiffness, damping 또는 mass가 설정되면 bounce와 duration은 무시됩니다.

```javascript
<motion.div
  animate={{ x: 100 }}
  transition={{ type: "spring", bounce: 0.25 }}
/>
```

### damping: number

반대 힘의 강도입니다. 0으로 설정하면 스프링이 무한히 진동합니다. 기본값은 10입니다.

```javascript
<motion.a
  animate={{ rotate: 180 }}
  transition={{ type: "spring", damping: 300 }}
/>
```

### mass: number

움직이는 물체의 질량입니다. 값이 높을수록 움직임이 더 둔해집니다. 기본값은 1입니다.

```javascript
<motion.feTurbulence
  animate={{ baseFrequency: 0.5 } as any}
  transition={{ type: "spring", mass: 0.5 }}
/>
```

### stiffness: number

스프링의 강성입니다. 값이 높을수록 더 급격한 움직임이 생깁니다. 기본값은 100입니다.

```javascript
<motion.section
  animate={{ rotate: 180 }}
  transition={{ type: "spring", stiffness: 50 }}
/>
```

### restSpeed: number

절대 속도(초당 단위)가 이 값 아래로 떨어지고 델타가 `restDelta`보다 작으면 애니메이션을 종료합니다. 기본값은 `0.01`입니다.

```javascript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ type: "spring", restSpeed: 0.5 }}
/>
```

### restDelta: number

거리가 이 값 아래이고 속도가 `restSpeed` 아래일 때 애니메이션을 종료합니다. 애니메이션이 끝나면 스프링이 "스냅"됩니다. 기본값은 `0.01`입니다.

```javascript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ type: "spring", restDelta: 0.5 }}
/>
```

## Inertia

관성 애니메이션은 초기 속도를 기반으로 값을 감속시키는 애니메이션으로, 주로 관성 스크롤을 구현하는 데 사용됩니다.

선택적으로 min과 max 경계를 정의할 수 있으며, 관성은 스프링 애니메이션을 사용하여 이 값들에 스냅됩니다.

이 애니메이션은 자동으로 목표 값을 미리 계산하며, 이는 modifyTarget 속성으로 수정할 수 있습니다.

이를 통해 그리드에 스냅하는 등의 기능을 추가할 수 있습니다.
관성은 또한 dragTransition에 사용되는 애니메이션이며, 해당 prop을 통해 구성할 수 있습니다.

```javascript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ type: "inertia", velocity: 50 }}
/>
```

### modifyTarget(v): number

자동으로 계산된 목표를 받아 새로운 목표를 반환하는 함수입니다. 목표를 그리드에 스냅하는 데 유용합니다.

```javascript
<motion.div
  drag
  dragTransition={{
    power: 0,
    // Snap calculated target to nearest 50 pixels
    modifyTarget: (target) => Math.round(target / 50) * 50,
  }}
/>
```

### bounceStiffness: number

`min` 또는 `max`가 설정된 경우, 이는 바운스 스프링의 강성에 영향을 줍니다. 값이 높을수록 더 급격한 움직임이 생깁니다. 기본값은 `500`입니다.

```javascript
<motion.div
  drag
  dragTransition={{
    min: 0,
    max: 100,
    bounceStiffness: 100,
  }}
/>
```

### bounceDamping: number

`min` 또는 `max`가 설정된 경우, 이는 바운스 스프링의 감쇠에 영향을 줍니다. 0으로 설정하면 스프링이 무한히 진동합니다. 기본값은 `10`입니다.

```javascript
<motion.div
  drag
  dragTransition={{
    min: 0,
    max: 100,
    bounceDamping: 8,
  }}
/>
```

### power: number

power 값이 높을수록 목표가 더 멀어집니다. 기본값은 0.8입니다.

```javascript
<motion.div drag dragTransition={{ power: 0.2 }} />
```

### timeConstant: number

시간 상수를 조정하면 감속의 지속 시간이 변경되어 느낌에 영향을 줍니다. 기본값은 700입니다.

```javascript
<motion.div drag dragTransition={{ timeConstant: 200 }} />
```

### restDelta: number

애니메이션 목표까지의 거리가 이 값 미만이고 절대 속도가 restSpeed 미만인 경우 애니메이션을 종료합니다. 애니메이션이 끝나면 값이 애니메이션 목표로 스냅됩니다. 기본값은 0.01입니다. 일반적으로 기본값은 부드러운 애니메이션 종료를 제공하며, 드문 경우에만 이를 사용자 정의할 필요가 있습니다.

```javascript
<motion.div drag dragTransition={{ restDelta: 10 }} />
```

### min: number

최소 제약 조건입니다. 설정된 경우, 값은 이 값에 "부딪힙니다" (또는 애니메이션이 이 값보다 작은 값으로 시작하면 즉시 이 값으로 스프링됩니다).

```javascript
<motion.div drag dragTransition={{ min: 0, max: 100 }} />
```

### max: number

최대 제약 조건입니다. 설정된 경우, 값은 이 값에 "부딪힙니다" (또는 초기 애니메이션 값이 이 값을 초과하면 즉시 이 값으로 스냅됩니다).

```javascript
<motion.div drag dragTransition={{ min: 0, max: 100 }} />
```

## Miscellaneous

### TargetAndTransition

애니메이션할 목표 값을 지정하는 객체입니다. 각 값은 단일 값이나 값의 배열로 설정될 수 있습니다.
또한 이 객체는 다음 속성들을 포함할 수 있습니다:

- `transition`: 모든 값 또는 개별 값에 대한 전환을 지정합니다.
- `transitionEnd`: 애니메이션이 끝날 때 설정할 값을 지정합니다.

```javascript
const target = {
  x: "0%",
  opacity: 0,
  transition: { duration: 1 },
  transitionEnd: { display: "none" },
};
```
