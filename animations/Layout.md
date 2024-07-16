## Layout animations

### React와 Framer Motion을 사용한 레이아웃 및 공유 레이아웃 애니메이션 만들기.

CSS 레이아웃은 애니메이션을 적용하기 어렵고 비용이 많이 듭니다.

height를 100px에서 500px로 애니메이션 처리하는 것은 개념적으로는 간단하지만, 매 애니메이션 프레임마다 브라우저의 레이아웃 시스템을 트리거하기 때문에 성능이 좋지 않습니다.

때로는 이해하기 어려운 경우도 있습니다. justify-content를 flex-start에서 flex-end로 애니메이션 처리한다는 것이 실제로 무엇을 의미할까요?

Framer Motion은 레이아웃 시스템 대신 성능이 좋은 transform을 사용하여 모든 CSS 레이아웃 간의 애니메이션을 만들 수 있습니다.

예를 들어, 이 컴포넌트는 justify-content를 flex-start와 flex-end 사이에서 전환하여 애니메이션을 적용합니다.

[![Edit framer-motion-2-layout-animations](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/framer-motion-2-layout-animations-kij8p?fontsize=14&hidenavigation=1&theme=dark)

Framer Motion의 레이아웃 애니메이션을 활성화하려면 motion 컴포넌트에 layout 속성을 설정하면 됩니다.

```javascript
<motion.div layout />
```

리렌더링으로 인한 모든 레이아웃 변경이 애니메이션 처리됩니다.
이는 다음과 같은 조합일 수 있습니다:

- 리스트의 순서 변경
- 컴포넌트 자체에 설정된 스타일 변경 (예: width 또는 position 변경)
- 부모의 레이아웃 변경 (예: flexbox 또는 grid)
- 또는 컴포넌트 레이아웃의 기타 모든 변경

### 스케일 보정

모든 레이아웃 애니메이션은 transform 속성을 사용하여 수행되며, 이로 인해 부드러운 프레임 속도를 얻을 수 있습니다.

transform을 사용한 레이아웃 애니메이션은 때때로 자식 요소를 시각적으로 왜곡할 수 있습니다. 이러한 왜곡을 보정하기 위해, 해당 요소의 첫 번째 자식 요소에도 layout 속성을 부여할 수 있습니다.

핑크색 점에 layout을 설정한 경우와 설정하지 않은 경우를 비교하면서 이 컴포넌트의 레이아웃을 전환해 보세요:

[![Edit framer-motion-2-layout-animations](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/framer-motion-2-scale-correction-z4tgr?from-embed)

Transform은 boxShadow와 borderRadius도 왜곡할 수 있습니다. motion 컴포넌트는 이 두 속성이 모션 값으로 설정되어 있는 한 자동으로 이 왜곡을 보정합니다.
이 값들을 애니메이션하지 않는다면, style을 통해 설정하는 것이 가장 쉬운 방법입니다.

```javascript
<motion.div layout style={{ borderRadius: 20 }} />
```

### 레이아웃 애니메이션 커스터마이징

레이아웃 애니메이션은 transition 속성을 사용하여 커스터마이징할 수 있습니다.

```javascript
<motion.div layout transition={{ duration: 0.3 }} />
```

**레이아웃 애니메이션에만** 특정 전환을 설정하려면 layout 전환을 지정할 수 있습니다.

```
<motion.div
  layout
  animate={{ opacity: 0.5 }}
  transition={{
    opacity: { ease: "linear" },
    layout: { duration: 0.3 }
  }}
/>
```

### 스크롤 컨테이너 내에서 애니메이션

스크롤 가능한 요소 내에서 레이아웃을 올바르게 애니메이션하려면 이 요소에 layoutScroll 속성을 주어야 합니다.

```
<motion.div
  layoutScroll
  style={{ overflow: "scroll" }}
/>
```

이를 통해 Framer Motion은 자식 요소를 측정할 때 이 요소의 스크롤 오프셋을 고려할 수 있습니다.

### 레이아웃 애니메이션 조정

레이아웃 애니메이션은 컴포넌트가 리렌더링되고 레이아웃이 변경되었을 때 트리거됩니다.

```javascript
function Accordion() {
  const [isOpen, setOpen] = useState(false);
  return (
    <motion.div
      layout
      style={{ height: isOpen ? "100px" : "500px" }}
      onClick={() => setOpen(!isOpen)}
    />
  );
}
```

하지만 동시에 리렌더링되지 않지만 서로의 레이아웃에 영향을 미치는 두 개 이상의 컴포넌트가 있을 때는 어떻게 될까요?

```javascript
function List() {
  return (
    <>
      <Accordion />
      <Accordion />
    </>
  );
}
```

하나가 리렌더링될 때 다른 하나는 자신의 레이아웃 변화를 감지할 수 없습니다.
여러 컴포넌트 간의 레이아웃 변경을 동기화하기 위해 LayoutGroup component로 감싸줄 수 있습니다.

```javascript
import { LayoutGroup } from "framer-motion";

function List() {
  return (
    <LayoutGroup>
      <Accordion />
      <Accordion />
    </LayoutGroup>
  );
}
```

이제 그룹화된 한 컴포넌트에서 레이아웃 변경이 감지되면 모든 컴포넌트에서 레이아웃 애니메이션이 발생합니다. 추가적인 리렌더링 없이 말이죠.

### 공유 레이아웃 애니메이션

기존 컴포넌트와 일치하는 layoutId 속성을 가진 새 컴포넌트가 추가되면 자동으로 이전 컴포넌트에서 애니메이션이 시작됩니다.

```javascript
isSelected && <motion.div layoutId="underline" />;
```

[![Edit framer-motion-2-layout-animations](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/framer-motion-layout-animations-snxgv?from-embed)

새 컴포넌트가 들어올 때 기존 컴포넌트가 여전히 마운트된 상태라면, 자동으로 이전 컴포넌트에서 새 컴포넌트로 크로스페이드 애니메이션이 적용됩니다.

### Trouble shooting

**컴포넌트가 애니메이션되지 않음**

컴포넌트가 display: inline으로 설정되어 있지 않은지 확인하세요. 브라우저는 이러한 요소에 transform을 적용하지 않습니다.

**SVG 레이아웃 애니메이션이 깨짐**

SVG 컴포넌트는 현재 레이아웃 애니메이션을 지원하지 않습니다. SVG는 레이아웃 시스템이 없으므로 cx 등의 속성을 직접 애니메이션하는 것이 좋습니다.

**Skew 변형이 적용되지 않음**

skew 변형은 현재 레이아웃 애니메이션과 호환되지 않습니다.

**콘텐츠가 원치 않게 늘어남**

이는 스케일로 너비와 높이를 애니메이션할 때의 자연스러운 부작용입니다. 서로 다른 종횡비로 변경되는 요소를 포함하는 경우(일반적으로 텍스트 요소), layout="position"을 사용하여 요소의 위치만 애니메이션하는 것이 더 나을 수 있습니다.

**테두리 반경이나 박스 그림자가 이상하게 동작함**

scale 애니메이션은 성능이 좋지만 border-radius와 box-shadow 같은 스타일을 왜곡할 수 있습니다.
Framer Motion은 이 스케일 왜곡을 자동으로 보정하지만, 이 보정은 픽셀이나 퍼센트로 정의된 border-radius와 단일 box-shadow로 제한됩니다.

**고정 요소가 예상대로 애니메이션되지 않음**

position: sticky 요소는 두 좌표 공간(페이지 상대 및 뷰포트 상대) 사이를 전환하며, 어떤 것을 사용해야 할지 감지할 방법이 없어 애니메이션하기 어렵습니다.
position: sticky 요소의 자식이 예상대로 애니메이션되지 않는 경우, 고정 요소에 layout layoutRoot 속성을 추가할 수 있습니다. 이렇게 하면 sticky 요소에 대해 모든 레이아웃 계산과 즉각적인 레이아웃 애니메이션이 수행되며, 모든 자식 요소는 이에 상대적으로 레이아웃 애니메이션을 수행합니다.

```javascript
<motion.header layout layoutRoot style={{ position: "sticky" }}>
  <motion.h1 layout />
</motion.header>
```

**주변 콘텐츠가 변경될 때 아이템이 원치 않는 레이아웃 애니메이션을 수행함**

부모에 layout layoutRoot를 추가하면 자식들이 부모에 상대적으로 모든 레이아웃 애니메이션을 수행합니다. 예시를 참조하세요.

```javascript
<motion.div className="switch-container" layout layoutRoot>
  <motion.div className="switch-handle" layout />
</motion.div>
```
