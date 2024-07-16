# LayoutGroup

함께 레이아웃 애니메이션을 수행해야 하는 모션 컴포넌트를 그룹화하세요.

기본적으로 `layout` prop을 가진 `motion` 컴포넌트는 React 렌더링을 수행할 때마다 레이아웃 변경을 감지하고 애니메이션을 시도합니다.
다른 트리의 컴포넌트가 서로의 레이아웃에 영향을 줄 수 있는 경우가 있습니다.

```javascript
function ToggleContent({ header, content }) {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <motion.div layout onClick={() => setIsOpen(!isOpen)}>
      <motion.h2 layout>{header}</motion.h2>
      {isOpen ? content : null}
    </motion.div>
  );
}

function App() {
  return (
    <>
      <ToggleContent />
      <ToggleContent />
    </>
  );
}
```

이러한 `ToggleContent` 컴포넌트 중 하나의 상태가 변경되면, 해당 컴포넌트의 형제 컴포넌트는 다시 렌더링되지 않으므로 레이아웃 애니메이션이 수행되지 않습니다.
이 문제는 두 컴포넌트를 `LayoutGroup`으로 그룹화하여 해결할 수 있습니다.

```javascript
import { LayoutGroup } from "framer-motion";

function App() {
  return (
    <LayoutGroup>
      <ToggleContent />
      <ToggleContent />
    </LayoutGroup>
  );
}
```

이제 `LayoutGroup` 내의 하나의 `layout` 컴포넌트가 레이아웃 변경을 감지하고 애니메이션을 수행하면, 모든 컴포넌트가 동일하게 수행합니다.

## Namespace `layoutId`

공유 레이아웃 애니메이션을 수행하도록 기대되는 컴포넌트에는 `layoutId` prop이 제공됩니다.

```javascript
function Tab({ label, isSelected }) {
  return (
    <li>
      {label}
      {isSelected ? <motion.div layoutId="underline" /> : null}
    </li>
  );
}

function TabRow({ items }) {
  return items.map((item) => <Tab {...item} />);
}
```

`layoutId`는 전역적으로 사용되기 때문에, 여러 개의 `TabRow` 인스턴스가 렌더링되더라도 `layoutId="underline"`을 가진 하나의 컴포넌트만 한 번에 렌더링됩니다.

이 문제를 해결하려면, `LayoutGroup`에 `id` prop을 제공하여 내부의 모든 컴포넌트에 대한 `layoutId`의 네임스페이스를 지정할 수 있습니다.

```javascript
function App() {
  return (
    <LayoutGroup id="top5">
      <TabRow />
    </LayoutGroup>
    <LayoutGroup id="latest">
      <TabRow />
    </LayoutGroup>
  )
}

```
