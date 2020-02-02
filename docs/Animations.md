# **Animations**

> React Native 의 [Docs > Animations](https://facebook.github.io/react-native/docs/animations) 의 번역 문서입니다. 번역 오류는 댓글로 남겨 주세요. 원문을 보시기를 추천드립니다.

훌륭한 사용자 경험을 위해서는 애니메이션이 매우 중요합니다. 정지된 물체는 움직이기 시작할 때 관성을 극복해야합니다. 움직이는 물체는 운동량을 가졌으며 거의 즉시 멈추지 않습니다. 애니메이션을 사용하면 인터페이스에서 물리적으로 믿을 수있는 동작을 전달할 수 있습니다.

React Native는 두 가지 보완 애니메이션 시스템을 제공합니다. React Native는 특정 값을 세부적으로 대화식으로 제어 할 수있는 2 개의 보완 애니메이션 시스템: [Animated](#animated-api)와 애니메이션 글로벌 레이아웃 트랜잭션을위한 [LayoutAnimation](#layoutanimation-api)을 제공합니다.

&nbsp;

## **Animated API**

[Animated](https://github.com/ABoutCoDing/React-Native-Docs/blob/master/apis/Animated.md) API는 매우 흥미로운 방식으로 다양한 흥미로운 애니메이션 및 상호 작용 패턴을 간결하게 표현하도록 설계되었습니다. `Animated`는 시간 기반 애니메이션(time-based animation) 실행을 제어하기 위해 구성 가능한 변환 및 `star/stop` 메서드를 사용하여 입력과 출력 간의 선언적 관계에 중점을 둡니다.

`Animated`는 6 개의 애니메이션 가능 구성 요소 유형 인 `View`, `Text`, `Image`, `ScrollView`, `FlatList` 및 `SectionList`를 내보내지만 `Animated.createAnimatedComponent()`를 사용하여 직접 만들 수도 있습니다.

예를 들어, 마운트 될 때 페이드 인(fades in)되는 컨테이너 뷰는 다음과 같습니다.

```javascript
import React, { useState, useEffect } from 'react';
import { Animated, Text, View } from 'react-native';

const FadeInView = (props) => {
  const [fadeAnim] = useState(new Animated.Value(0))  // Initial value for opacity: 0

  React.useEffect(() => {
    Animated.timing(
      fadeAnim,
      {
        toValue: 1,
        duration: 10000,
      }
    ).start();
  }, [])

  return (
    <Animated.View                 // Special animatable View
      style={{
        ...props.style,
        opacity: fadeAnim,         // Bind opacity to animated value
      }}
    >
      {props.children}
    </Animated.View>
  );
}

// You can then use your `FadeInView` in place of a `View` in your components:
export default () => {
  return (
    <View style={{flex: 1, alignItems: 'center', justifyContent: 'center'}}>
      <FadeInView style={{width: 250, height: 50, backgroundColor: 'powderblue'}}>
        <Text style={{fontSize: 28, textAlign: 'center', margin: 10}}>Fading in</Text>
      </FadeInView>
    </View>
  )
}
```

여기서 무슨 일이 일어나고 있는지 봅시다. `FadeInView` 생성자에서 fadeAnim이라는 새로운 `Animated.Value`가 state의 일부로 초기화됩니다. `View`의 불투명도 속성이 이 애니메이션 값에 매핑됩니다. 씬(scense) 뒤에서 숫자 값이 추출되어 불투명도를 설정하는데 사용됩니다.

구성 요소가 마운트되면 불투명도가 0으로 설정됩니다. 그다음 `fadeAnim` 애니메이션 값에서 easing animation이 시작되어 값이 최종 값 1로 애니메이션 될 때 각 프레임의 모든 종속 매핑 (이 경우 불투명도 만)이 업데이트됩니다.

이것은 `setState`를 호출하고 다시 렌더링하는 것보다 빠른 최적화 된 방식으로 수행됩니다. 전체 구성이 선언적이므로 구성을 직렬화하고 우선 순위가 높은 스레드에서 애니메이션을 실행하는 추가 최적화를 구현할 수 있습니다.

&nbsp;

### Configuring animations

애니메이션은 과하게(heavily) 구성 할 수 있습니다. 애니메이션 유형에 따라 커스텀 및 사전 정의 된(predefined) easing functions(여유 함수), 지연(delays), 지속 시간(durations), 감쇠 계수(decay factors), 스프링 상수(spring constants) 등을 모두 조정할 수 있습니다.

`Animated`는 여러 애니메이션 유형을 제공하며 가장 일반적으로 사용되는 애니메이션 유형은 [Animated.timing()](https://facebook.github.io/react-native/docs/animated#timing)입니다. 미리 정의 된 다양한 여유 함수 중 하나를 사용하여 시간에 따른 값 애니메이션을 지원하거나 직접 사용할 수 있습니다. Easing functions은 일반적으로 오브젝트의 점진적인 가속 및 감속을 전달하기 위해 애니메이션에서 사용됩니다.

기본적으로 `timing`은 점진적 가속을 최고 속도(full speed)로 전달하고 점차적으로 정지 위치까지 감속하는 easeInOut 곡선을 사용합니다. `easing parameter(여유 매개 변수)`를 전달하여 다른 Easing functions을 지정할 수 있습니다. 커스텀 `duration(지속)` 시간 또는 애니메이션 시작 전 `delay(지연)`도 지원됩니다.

예를 들어, 최종 위치로 이동하기 전에 약간 백업되는 객체의 2초 길이(2-second)의 애니메이션을 만들려는 경우:

```javascript
Animated.timing(this.state.xPosition, {
  toValue: 100,
  easing: Easing.back(),
  duration: 2000,
}).start();
```

기본 제공 애니메이션에서 지원하는 모든 구성 매개 변수에 대해 자세히 알려면 `Animated` API 참조의 [애니메이션 구성]([https://github.com/ABoutCoDing/React-Native-Docs/blob/master/apis/Animated.md#%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98-%EA%B5%AC%EC%84%B1-configuring-animations](https://github.com/ABoutCoDing/React-Native-Docs/blob/master/apis/Animated.md#애니메이션-구성-configuring-animations)) 섹션을 살펴보십시오.

&nbsp;

### 애니메이션 구성 (Composing animations)

애니메이션은 순서대로 또는 병렬로 결합하여 재생할 수 있습니다. 순차적 애니메이션은 이전 애니메이션이 끝난 직후에 재생되거나 지정된 지연 후에 시작될 수 있습니다. `Animated` API는 `sequence()` 및 `delay()`과 같은 몇 가지 메소드를 제공합니다.  각 메소드는 애니메이션을 실행하고 필요에 따라 자동으로 `start()` / `stop()`을 호출합니다.

예를 들어, 다음 애니메이션은 정지 지점까지 이어지고 동시에 빙글 빙글 돌리면서 튀어 나옵니다.

```javascript
Animated.sequence([
  // decay, then spring to start and twirl
  Animated.decay(position, {
    // coast to a stop
    velocity: {x: gestureState.vx, y: gestureState.vy}, // velocity from gesture release
    deceleration: 0.997,
  }),
  Animated.parallel([
    // after decay, in parallel:
    Animated.spring(position, {
      toValue: {x: 0, y: 0}, // return to start
    }),
    Animated.timing(twirl, {
      // and twirl
      toValue: 360,
    }),
  ]),
]).start(); // start the sequence group
```

하나의 애니메이션이 중지되거나 중단되면 그룹의 다른 모든 애니메이션도 중지됩니다. `Animated.parallel`에는 이를 비활성화하기 위해 `false`로 설정할 수있는 `stopTogether` 옵션이 있습니다.

애니메이션 API 참조의 [Composing animations(애니메이션 구성)]([https://github.com/ABoutCoDing/React-Native-Docs/blob/master/apis/Animated.md#%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98-%EA%B5%AC%EC%84%B1-composing-animations](https://github.com/ABoutCoDing/React-Native-Docs/blob/master/apis/Animated.md#애니메이션-구성-composing-animations)) 섹션에서 컴포지션 메서드의 전체 목록을 찾을 수 있습니다.

&nbsp;

### **애니메이션 값 결합 (Combining animated values)**

더하기, 곱하기, 나누기 또는 모듈로를 통해 [두 개의 애니메이션 값을 결합(combine two animated values)](https://facebook.github.io/react-native/docs/animated#combining-animated-values)하여 새로운 애니메이션 값을 만들 수 있습니다.

움직이는(animate) 값의 계산을 위해 다른 애니메이션 값을 반전시켜야하는 경우가 있습니다. 예를 들어 스케일 반전 (2x --> 0.5x)이 있습니다.

```javascript
const a = new Animated.Value(1);
const b = Animated.divide(1, a);

Animated.spring(a, {
  toValue: 2,
}).start();
```

&nbsp;

### **보간 (Interpolation)**

각 속성은 먼저 보간을 통해 실행될 수 있습니다. 보간은 일반적으로 선형 보간을 사용하여 입력 범위를 출력 범위에 매핑하지만  easing functions 도 지원합니다. 기본적으로 주어진 범위를 넘어서 곡선을 추정하지만 출력 값을 고정시킬 수도 있습니다.

0-1 범위를 0-100 범위로 변환하는 매핑은 다음과 같습니다.

```javascript
value.interpolate({
  inputRange: [0, 1],
  outputRange: [0, 100],
});
```

예를 들어 `Animated.Value`를 0에서 1로 생각하지만 위치를 150px에서 0px로, 불투명도를 0에서 1로 애니메이션 할 수 있습니다. 위의 예제에서 `style(스타일)`을 다음과 같이 수정하면됩니다.

```javascript
  style={{
    opacity: this.state.fadeAnim, // Binds directly
    transform: [{
      translateY: this.state.fadeAnim.interpolate({
        inputRange: [0, 1],
        outputRange: [150, 0]  // 0 : 150, 0.5 : 75, 1 : 0
      }),
    }],
  }}
```

[interpolate()](https://facebook.github.io/react-native/docs/animated#interpolate)는 다중 범위 세그먼트도 지원하므로 데드 존 및 기타 편리한 트릭을 정의하는 데 편리합니다. 예를 들어 -300에서 -100, 0으로, 0에서 1로, 100에서 0으로 그 이후의 모든 부분에 대해 0으로 유지되는 데드 존 (dead-zone)으로 돌아가는 부정 관계(negation relationship)를 얻으려면, 이렇게 할 수 있습니다 :

```javascript
value.interpolate({
  inputRange: [-300, -100, 0, 100, 101],
  outputRange: [300, 0, 1, 0, 0],
});
```

다음과 같이 매핑됩니다.

```javascript
Input | Output
------|-------
  -400|    450
  -300|    300
  -200|    150
  -100|      0
   -50|    0.5
     0|      1
    50|    0.5
   100|      0
   101|      0
   200|      0
```

`interpolate()`는 문자열에 대한 매핑도 지원하므로 색상과 단위 값을 애니메이션 할 수 있습니다. 예를 들어 회전(rotation)에 애니메이션을 적용하려면 다음을 수행하십시오.

```javascript
value.interpolate({
  inputRange: [0, 360],
  outputRange: ['0deg', '360deg'],
});
```

`interpolate()`는 임의의 easing functions(여유 함수)도 지원하며,이 중 대부분은 이미 Easing 모듈에서 구현되어 있습니다. `interpolate()`에는 `outputRange`를 추정하기 위해 구성 가능한 동작이 있습니다. extrapolate(외삽), extrapolateLeft 또는 extrapolateRight 옵션을 설정하여 extrapolation(외삽),을 설정할 수 있습니다. 기본값은 확장이지만 출력 값이 `outputRange`를 초과하지 않도록 `clamp`를 사용할 수 있습니다.

&nbsp;

### **동적 값 추적 (Tracking dynamic values)**

애니메이션 값은 다른 값을 추적 할 수도 있습니다. 애니메이션의 `toValue`를 일반 숫자 대신 다른 움직이는 값으로 설정하십시오. 예를 들어 Android에서 Messenger가 사용하는 것과 같은 "Chat Heads" 애니메이션은 다른 애니메이션 값에 고정 된 `spring()`으로, 또는 엄격한 추적을 위해 `timing()` 및 `duration(지속 시간)`을 0으로 구현할 수 있습니다. 보간으로 구성 할 수도 있습니다.

```javascript
Animated.spring(follower, {toValue: leader}).start();
Animated.timing(opacity, {
  toValue: pan.x.interpolate({
    inputRange: [0, 300],
    outputRange: [1, 0],
  }),
}).start();
```

`leader` 및 `follower` 애니메이션 값은 `Animated.ValueXY()`를 사용하여 구현됩니다. `ValueXY`는 panning(이동) 또는 dragging(끌기)과 같은 2D 상호 작용을 처리하는 편리한 방법입니다. 두 개의 Animated.Value 인스턴스와 이를 호출하는 일부 헬퍼 함수(helper functions)를 포함하는 래퍼로, `ValueXY`를 `Value`의 대체품으로 만드는 경우가 많습니다. 위 예제에서 x와 y 값을 모두 추적 할 수 있습니다.

&nbsp;

### **제스처 추적(Tracking gestures)**

이동 또는 스크롤과 같은 제스처 및 기타 이벤트는 [Animated.event](https://facebook.github.io/react-native/docs/animated#event)를 사용하여 애니메이션 값에 직접 매핑 될 수 있습니다. 복잡한 이벤트 객체에서 값을 추출 할 수 있도록 구조화 된 맵 구문으로 수행됩니다.  첫 번째 수준은 여러 인수에 대한 매핑을 허용하는 배열이며 해당 배열에는 중첩 된 개체가 포함됩니다.

예를 들어 가로 스크롤 제스처로 작업 할 때 `event.nativeEvent.contentOffset.x`를 `scrollX` (`Animated.Value`)에 매핑하려면 다음을 수행하십시오.

```javascript
 onScroll={Animated.event(
   // scrollX = e.nativeEvent.contentOffset.x
   [{ nativeEvent: {
        contentOffset: {
          x: scrollX
        }
      }
    }]
 )}
```

`PanResponder`를 사용할 때 다음 코드를 사용하여 `gestureState.dx` 및 `gestureState.dy`에서 x 및 y 위치를 추출 할 수 있습니다. `PanResponder` 핸들러 (`gestureState`)에 전달 된 두 번째 인수에만 관심이 있으므로 배열의 첫 번째 위치에서 `null`을 사용합니다.

```javascript
onPanResponderMove={Animated.event(
  [null, // ignore the native event
  // extract dx and dy from gestureState
  // like 'pan.x = gestureState.dx, pan.y = gestureState.dy'
  {dx: pan.x, dy: pan.y}
])}
```

&nbsp;

### **현재 애니메이션 값에 응답 (Responding to the current animation value)**

애니메이션하는 동안 현재 값을 읽을 수있는 명확한 방법이 없다는 것을 알 수 있습니다. 최적화로 인해 기본 런타임에서만 값을 알 수 있기 때문입니다. 현재 값에 대한 응답으로 JavaScript를 실행해야하는 경우 다음 두 가지 방법이 있습니다.

- `spring.stopAnimation(callback)`은 애니메이션을 멈추고 최종 값으로 `callback`을 호출합니다. 제스처 전환을 할 때 유용합니다.
- `spring.addListener(callback)`은 애니메이션이 실행되는 동안 `callback`을 비동기 적으로 호출하여 최근 값을 제공합니다. 이는 상태 변경을 트리거하는 데 유용합니다. 예를 들어 사용자가 더 가까이 드래그 할 때 보블을 새 옵션에 스냅하면 이러한 큰 상태 변경은 60fps에서 실행해야하는 패닝과 같은 연속 동작에 비해 몇 프레임 지연에 덜 민감합니다. 

`Animated`는 일반 JavaScript 이벤트 루프와 상관없이 애니메이션을 고성능 방식으로 실행할 수 있도록 완전히 직렬화 할 수 있도록 설계되었습니다. 이는 API에 영향을 미치므로 완전히 동기화 된 시스템과 비교할 때 약간 까다로움을 염두에 두십시오. 이러한 제한 사항 중 일부를 해결하는 방법으로 `Animated.Value.addListener`를 확인하되 나중에 성능에 영향을 줄 수 있으므로 조금만 사용하십시오.

&nbsp;

### **네이티브 드라이브 사용 (Using the native driver)**

Animated API는 직렬화 가능하도록 설계되었습니다. [native driver(네이티브 드라이버)](http://facebook.github.io/react-native/blog/2017/02/14/using-native-driver-for-animated)를 사용하면 애니메이션을 시작하기 전에 애니메이션에 대한 모든 것을 네이티브로 보내어 네이티브 코드가 모든 프레임에서 브리지를 거치지 않고도 UI 스레드에서 애니메이션을 수행 할 수 있습니다. 애니메이션이 시작되면 애니메이션에 영향을주지 않고 JS 스레드를 차단(blocked) 할 수 있습니다.

일반 애니메이션에 기본 드라이버를 사용하려면 애니메이션 구성을 시작할 때 `useNativeDriver : true`를 추가하십시오.

```javascript
Animated.timing(this.state.animatedValue, {
  toValue: 1,
  duration: 500,
  useNativeDriver: true, // <-- Add this
}).start();
```

애니메이션 값은 하나의 드라이버와 만 호환되므로 값에서 애니메이션을 시작할 때 기본 드라이버를 사용하는 경우 해당 값의 모든 애니메이션도 기본 드라이버를 사용해야합니다.

기본 드라이버는 `Animated.event` 와도 작동합니다. 이 기능은 네이티브 드라이버 없이 스크롤 위치를 따르는 애니메이션에 특히 유용합니다.  애니메이션은 React Native의 비동기 특성으로 인해 항상 제스처 뒤에서 프레임을 실행합니다.

```javascript
<Animated.ScrollView // <-- Use the Animated ScrollView wrapper
  scrollEventThrottle={1} // <-- Use 1 here to make sure no events are ever missed
  onScroll={Animated.event(
    [
      {
        nativeEvent: {
          contentOffset: {y: this.state.animatedValue},
        },
      },
    ],
    {useNativeDriver: true}, // <-- Add this
  )}>
  {content}
</Animated.ScrollView>
```

[RNTester](https://github.com/facebook/react-native/blob/master/RNTester/) 앱을 실행 한 다음 Native Animated Example을로드하여 기본 드라이버가 작동 중인지 확인할 수 있습니다. [소스 코드](https://github.com/facebook/react-native/blob/master/RNTester/js/NativeAnimationsExample.js)를 살펴보고 이러한 예제가 어떻게 생성되었는지 알아볼 수도 있습니다.

##### 경고 (Caveats)

현재 `Animated`로 수행 할 수있는 모든 기능이 기본 드라이버에서 지원되는 것은 아닙니다. 주요 제한 사항은 레이아웃이 아닌 속성에만 애니메이션을 적용 할 수 있다는 것입니다. transform(변형) 및 opacity(불투명도)와 같은 기능은 작동하지만 flexbox 및 위치 속성은 작동하지 않습니다. `Animated.event`를 사용하면 버블링 이벤트가 아닌 직접 이벤트에서만 작동합니다. 이것은 `PanResponder`와는 작동하지 않지만 `ScrollView#onScroll`과 같은 것에서는 작동합니다.

애니메이션이 실행 중일 때 `VirtualizedList` 구성 요소가 더 많은 행을 렌더링하지 못하게 할 수 있습니다. 사용자가 목록을 스크롤하는 동안 길거나 반복되는 애니메이션을 실행해야하는 경우 애니메이션 구성에서 `isInteraction : false`를 사용하여이 문제를 방지 할 수 있습니다.

&nbsp;

### **명심하십시오 (Bear in mind)**

`rotateY`, `rotateX` 등과 같은 변형 스타일을 사용하는 동안 변형 스타일의 `perspective(원근법)`이 적용됩니다. 현재 일부 애니메이션이 없으면 Android에서 렌더링되지 않을 수 있습니다. 아래 예.

```javascript
<Animated.View
  style={{
    transform: [
      {scale: this.state.scale},
      {rotateY: this.state.rotateY},
      {perspective: 1000}, // without this line this Animation will not render on Android while working fine on iOS
    ],
  }}
/>
```

&nbsp;

### **Additional examples**

RNTester 앱에는 다양한 애니메이션 사용 예가 있습니다.

- [AnimatedGratuitousApp](https://github.com/facebook/react-native/tree/master/RNTester/js/examples/Animated/AnimatedGratuitousApp)
- [NativeAnimationsExample](https://github.com/facebook/react-native/blob/master/RNTester/js/examples/NativeAnimation/NativeAnimationsExample.js)

&nbsp;

## **LayoutAnimation API**

`LayoutAnimation`을 사용하면 다음 render/layout 주기에서 모든 뷰에 사용될 애니메이션 `create` 및 `update`를 전체적으로 구성 할 수 있습니다. 

이는 특정 속성을 직접 애니메이션으로 표시하기 위해 특정 속성을 측정하거나 계산하지 않고 flexbox 레이아웃 업데이트를 수행하는 데 유용하며, 레이아웃 변경 사항이 상위 항목에 영향을 줄 수 있는 경우 예를 들어, 부모의 크기를 늘리고 아래 행을 아래로 내리는 "더보기" 확장을 사용하면 구성 요소를 모두 동기화하기 위해 구성 요소간에 명시적인 조정(coordination)이 필요합니다.

`LayoutAnimation`은 매우 강력하고 유용 할 수 있지만 Animated 및 기타 애니메이션 라이브러리보다 훨씬 적은 제어 기능을 제공하므로 `LayoutAnimation`을 사용하여 원하는 작업을 수행 할 수없는 경우 다른 방법을 사용해야합니다.

**Android**에서 이 기능을 사용하려면 `UIManager`를 통해 다음 플래그를 설정해야합니다.

```javascript
UIManager.setLayoutAnimationEnabledExperimental &&
  UIManager.setLayoutAnimationEnabledExperimental(true);
```

```javascript
import React from 'react';
import {
  NativeModules,
  LayoutAnimation,
  Text,
  TouchableOpacity,
  StyleSheet,
  View,
} from 'react-native';

const { UIManager } = NativeModules;

UIManager.setLayoutAnimationEnabledExperimental &&
  UIManager.setLayoutAnimationEnabledExperimental(true);

export default class App extends React.Component {
  state = {
    w: 100,
    h: 100,
  };

  _onPress = () => {
    // Animate the update
    LayoutAnimation.spring();
    this.setState({w: this.state.w + 15, h: this.state.h + 15})
  }

  render() {
    return (
      <View style={styles.container}>
        <View style={[styles.box, {width: this.state.w, height: this.state.h}]} />
        <TouchableOpacity onPress={this._onPress}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>Press me!</Text>
          </View>
        </TouchableOpacity>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  box: {
    width: 200,
    height: 200,
    backgroundColor: 'red',
  },
  button: {
    backgroundColor: 'black',
    paddingHorizontal: 20,
    paddingVertical: 15,
    marginTop: 15,
  },
  buttonText: {
    color: '#fff',
    fontWeight: 'bold',
  },
});
```

이 예제는 사전 설정 값을 사용하며 필요에 따라 애니메이션을 사용자 정의 할 수 있습니다. 자세한 내용은 [LayoutAnimation.js](https://github.com/facebook/react-native/blob/master/Libraries/LayoutAnimation/LayoutAnimation.js)를 참조하십시오.

&nbsp;

## Additional notes

### **requestAnimationFrame**

`requestAnimationFrame`은 친숙한 브라우저의 폴리필(polyfill)입니다.  함수를 유일한 인수로 받아들인 다음 다시 그리기 전에 해당 함수를 호출합니다. 모든 JavaScript 기반 애니메이션 API의 기초가되는 애니메이션의 필수 구성 요소입니다.  일반적으로 직접 호출 할 필요는 없습니다. 애니메이션 API가 프레임 업데이트를 관리합니다.

&nbsp;

### **setNativeProps**

[직접 조작 섹션(Direct Manipulation section)](https://facebook.github.io/react-native/docs/direct-manipulation)에서 언급했듯이 `setNativeProps`를 사용하면 구성 요소 계층을 `setState`하고 다시 렌더링 할 필요없이 기본 지원 구성 요소 (복합 구성 요소와 달리 기본보기로 실제로 지원되는 구성 요소)의 속성을 직접 수정할 수 있습니다.

리바운드 예제(Rebound example)에서 이를 사용하여  scale-this(스케일)을 업데이트 할 수 있습니다.  업데이트하는 구성 요소가 깊이 중첩되어 `shouldComponentUpdate`로 최적화되지 않은 경우에 유용 할 수 있습니다. 프레임을 떨어뜨리고 (초당 60 프레임 미만으로 수행) 애니메이션을 찾으면 `setNativeProps` 또는 `shouldComponentUpdate`를 사용하여 최적화하십시오. 또는 [useNativeDriver 옵션](http://facebook.github.io/react-native/blog/2017/02/14/using-native-driver-for-animated)을 사용하여 JavaScript 스레드가 아닌 UI 스레드에서 애니메이션을 실행할 수 있습니다. [InteractionManager](https://facebook.github.io/react-native/docs/interactionmanager)를 사용하여 애니메이션이 완료 될 때까지 계산 집약적인 작업을 연기 할 수도 있습니다. 인앱(In-App) 개발자 메뉴 "FPS Monitor" 도구를 사용하여 프레임 속도를 모니터링 할 수 있습니다.
