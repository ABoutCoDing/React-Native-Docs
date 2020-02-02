# **Animated**

> React Native 의 [APIs > Animated](https://facebook.github.io/react-native/docs/animated) 의 번역 문서입니다. 번역 오류는 댓글로 남겨 주세요. 원문을 보시기를 추천드립니다.

`Animated` 라이브러리는 애니메이션을 유동적이고 강력하며 어렵지 않게(painless) 빌드하고 유지 관리하도록 설계되었습니다. `Animated`는 시간 기반 애니메이션(time-based animation) 실행을 제어하기 위해 구성 가능한 변환 및 `start(시작)`/`stop(중지)` 방법을 사용하여 입력과 출력 간의 선언적 관계에 중점을 둡니다.

애니메이션을 만드는 가장 기본적인 작업 과정은 `Animated.Value`를 만들고 애니메이션 컴포넌트의 하나 이상의 스타일 속성에 연결한 다음 `Animated.timing()`을 사용하여 애니메이션을 통해 업데이트를 유도하는 것입니다.

```javascript
Animated.timing(
 // Animate value over time
 this.state.fadeAnim, // The value to drive
 {
  toValue: 1, // Animate to final value of 1
 },
).start(); // Start the animation
```

애니메이션 사용의 추가 예를 보려면 [애니메이션](https://github.com/ABoutCoDing/React-Native-Docs/blob/master/docs/Animations.md#animated-api) 안내서를 참조하십시오.

&nbsp;

## **Overview**

`Animated` 와 함께 사용할 수있는 두 가지 값 유형이 있습니다.

- 단일 값에 대한 [Animated.Value()](#value)
- 벡터에 대한 [Animated.ValueXY()](#valuexy)

`Animated.Value`는 스타일 속성이나 다른 props에 바인딩 될 수 있으며 보간 될 수도 있습니다. 단일 `Animated.Value`는 여러 속성을 드라이브 할 수 있습니다.

&nbsp;

### 애니메이션 구성 (Configuring animations)

`Animated`는 세 가지 유형의 애니메이션 유형을 제공합니다. 각 애니메이션 유형은 값이 초기 값에서 최종 값으로 움직이는(animate) 방식을 제어하는 특정 애니메이션 곡선을 제공합니다.

- [Animated.decay()](#decay)는 초기 속도로 시작하여 점차 정지합니다. (gradually slows to a stop.)
- [Animated.spring()](#spring)은 기본 스프링 물리 모델(spring physics model)을 제공합니다. 
- [Animated.timing()](#timing)은 [easing functions](https://facebook.github.io/react-native/docs/easing)을 사용하여 시간이 지남에 따라 값을 animate 합니다.

대부분의 경우 `timing()`을 사용합니다. 기본적으로 오브젝트의 점진적인 가속을 최고 속도(full speed)로 전달하고 점차적으로 정지로 감속하여 끝나는 대칭(symmetric) easeInOut 곡선을 사용합니다.

&nbsp;

### **애니메이션 동작 (Working with animations)**

애니메이션에서 `start()`를 호출하면 애니메이션이 시작됩니다. `start()`는 애니메이션이 완료 될 때 호출되는 완료 콜백을 받습니다. 애니메이션이 정상적으로 실행되면 `{finished : true}`와 함께 완료 콜백이 호출됩니다. 애니메이션이 완료되기 전에 `stop()`이 호출되어 (예 : 제스처 또는 다른 애니메이션에 의해 중단 되었기 때문에) 애니메이션이 완료되면 `{finished : false}`를 받습니다.

&nbsp;

### **네이티브 드라이브 사용 (Using the native driver)**

네이티브 드라이버를 사용하여 애니메이션을 시작하기 전에 애니메이션에 대한 모든 것을 네이티브로 보내 네이티브 코드가 모든 프레임에서 브리지를 거치지 않고도 UI스레드에서 애니메이션을 수행 할 수 있습니다. 애니메이션이 시작되면 애니메이션에 영향을주지 않고 JS 스레드를 차단할 수 있습니다. 

애니메이션 구성에서 `useNativeDriver : true` 로 지정하여 기본 드라이버를 사용할 수 있습니다.  자세한 내용은 [Animation](https://github.com/ABoutCoDing/React-Native-Docs/blob/master/docs/Animations.md#네이티브-드라이브-사용-using-the-native-driver) 안내서를 참조하십시오.

&nbsp;

### 애니메이션 가능한 컴포넌트 (Animatable components)

애니메이션 가능한 컴포넌트만 움직이게(animate) 할 수 있습니다. 이런 고유한 컴포넌트는 애니메이션 값을 속성에 바인딩하는 마법을 수행하고 모든 프레임에서 react 렌더링 과 조정 프로세스의 코스트를 피하기 위해 기본 업데이트를 수행합니다.

- [createAnimatedComponent()](#createanimatedcomponent)를 사용하여 컴포넌트를 애니메이션 가능하게 만들 수 있습니다.

애니메이션은 위 래퍼를 사용하여 다음 애니메이션 가능 컴포넌트를 내보냅니다.

- Animated.Image 
- Animated.ScrollView
- Animated.Text
- Animated.View
- Animated.FlatList
- Animated.SectionList

&nbsp;

### **애니메이션 구성 (Composing animations)**

컴포지션 함수를 사용하여 애니메이션을 복잡한 방식으로 결합 할 수도 있습니다.

- [Animated.delay()](#delay)는 주어진 지연 후에 애니메이션을 시작합니다.
- [Animated.parallel()](#parallel)은 여러 애니메이션을 동시에 시작합니다.
- [Animated.sequence()](#sequence)는 애니메이션을 순서대로 시작하여 다음을 시작하기 전 각 애니메이션이 완료 될 때까지 기다립니다.
- [Animated.stagger()](#stagger)는 애니메이션을 순서와 병렬로 시작하지만 연속 지연됩니다.

한 애니메이션의 toValue를 다른 Animated.Value로 설정하여 애니메이션을 함께 연결할 수도 있습니다. 애니메이션 안내서의 [동적 값 추적(Tracking dynamic values)](https://facebook.github.io/react-native/docs/animations#tracking-dynamic-values)을 참조하십시오.

기본적으로 하나의 애니메이션이 중지되거나 중단되면 그룹의 다른 모든 애니메이션도 중지됩니다.

&nbsp;

### **애니메이션 값 결합 (Combining animated values)**

addition(더하기), subtraction(빼기), multiplication(곱하기), division(나누기) 또는 modulo(모듈로)를 통해 두 개의 애니메이션 값을 결합하여 새로운 애니메이션 값을 만들 수 있습니다.

- [Animated.add()](#add)
- [Animated.subtract()](#subtract)
- [Animated.divide()](#divide)
- [Animated.modulo()](#modulo)
- [Animated.multiply()](#multiply)

&nbsp;

### **보간 (Interpolation)**

interpolate() 함수를 사용하면 입력 범위를 다른 출력 범위에 매핑 할 수 있습니다. 기본적으로 주어진 범위를 넘어서 곡선을 추정하지만 출력 값을 고정시킬 수도 있습니다.

기본적으로 선형 보간을 사용하지만 easing functions도 지원합니다.

- [interpolate()](#interpolate)

[애니메이션](https://facebook.github.io/react-native/docs/animations#interpolation) 가이드에서 보간에 대해 자세히 알아보십시오.

&nbsp;

### **제스처 와 기타 이벤트 핸들링 (Handling gestures and other events)**

이동 또는 스크롤과 같은 제스처 및 기타 이벤트는 `Animated.event()`를 사용하여 애니메이션 값에 직접 매핑 될 수 있습니다. 복잡한 이벤트 객체에서 값을 추출 할 수 있도록 구조화 된 맵 구문으로 수행됩니다.

첫 번째 레벨은 여러 인수에 대한 매핑을 허용하는 배열이며 해당 배열에는 중첩된 오브젝트가 포함됩니다.

- [Animated.event()](#event)

예를 들어 가로 스크롤 제스처로 작업 할 때 event.nativeEvent.contentOffset.x를 scrollX (Animated.Value)에 매핑하려면 다음을 수행하십시오.

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

&nbsp;

# **Reference**

## **Methods**

주어진 값이 Value 대신 ValueXY 인 경우 각 구성 옵션은 스칼라 대신 {x : ..., y : ...} 형식의 벡터 일 수 있습니다.

&nbsp;

### **decay()**

```javascript
static decay(value, config)
```

감쇠 계수(decay coefficient)에 따라 초기 속도에서 0까지의 값을 애니메이션합니다.

구성(Config)은 다음 옵션이 있을 수있는 개체입니다.

- `velocity(속도)` : 초기 속도. 필수입니다.
- `deceleration(감속)` : 감쇠율(Rate of decay). 기본값은 0.997입니다.
- `isInteraction` : 이 애니메이션이 InteractionManager에서 "상호 작용 핸들(interaction handle)"을 작성하는지 여부입니다. 기본값은 true입니다.
- `useNativeDriver` : true 인 경우 기본 드라이버를 사용합니다. 기본값은 false입니다.

&nbsp;

### **timing()**

```javascript
static timing(value, config)
```

시간제 여유 곡선(timed easing curve)을 따라 값을 애니메이션합니다. 

[Easing](https://facebook.github.io/react-native/docs/easing) 모듈에는 수많은 사전 정의 된 곡선이 있거나 고유 한 기능을 사용할 수 있습니다.

구성은 다음 옵션이 있을 수있는 개체입니다.

- `duration` : 애니메이션 길이 (밀리 초). 기본값은 500입니다.
- `easing` : 곡선을 정의하는 Easing function. 기본값은 Easing.inOut (Easing.ease)입니다.
- `delay` : 지연 후 애니메이션을 시작합니다 (밀리 초). 기본값은 0입니다.
- `isInteraction` : 이 애니메이션이 InteractionManager에서 "상호 작용 핸들(interaction handle)"을 작성하는지 여부입니다. 기본값은 true입니다.
- `useNativeDriver` : true 인 경우 기본 드라이버를 사용합니다. 기본값은 false입니다.

&nbsp;

### **spring()**

```javscript
static spring(value, config)
```

감쇠 고조파 진동([damped harmonic oscillation](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator))을 기반으로 분석 스프링 모델에 따라 값을 애니메이션합니다. 

`toValue`가 업데이트 될 때 유체 상태(fluid motions)를 생성하기 위해 속도 상태를 추적하고 함께 연결할 수 있습니다.

구성은 다음 옵션이있을 수있는 개체입니다.

마찰(friction)/인장(tension) 또는 바운스(bounciness)/속도(speed) 옵션은 [Facebook Pop](https://github.com/facebook/pop), [Rebound](http://facebook.github.io/rebound/) 및 [Origami](http://origami.design/)의 스프링 모델과 일치합니다.

- `friction(마찰)` : "bounciness"/ overshoot를 제어합니다. 기본값 7.
- `tension(장력) `: 속도를 제어합니다. 기본값은 40입니다.
- `speed(속도)` : 애니메이션의 속도를 제어합니다. 기본 12.
- `bounciness` : 바운싱을 제어합니다. 기본값 8.

stiffness/damping/mass(강성 / 댐핑 / 질량)을 매개 변수로 지정하면 `Animated.spring`은 감쇠 고조파 발진기([damped harmonic oscillator](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator))의 운동 방정식을 기반으로 분석 스프링 모델을 사용합니다.

이 동작은 스프링 다이내믹스의 물리학에 약간 더 정확하고 충실하며 iOS의 CASpringAnimation에서 구현과 매우 유사합니다.

- `stiffness(강성)` : 스프링 강성 계수. 기본값은 100입니다.
- `damping(감쇠)` : 마찰력으로 인해 스프링의 움직임이 감쇠되는 방법을 정의합니다. 기본 10. 
- `mass(질량)` : 스프링 끝에 부착 된 물체의 질량. 기본

다른 구성 옵션은 다음과 같습니다.

- `velocity` : 스프링에 부착 된 물체의 초기 속도. 기본값 0 (object is at rest) 
- `overshootClamping` : 스프링이 클램핑되고 바운스되지 않아야하는지 여부를 나타내는 부울입니다. 기본값은 false입니다.
- `restDisplacementThreshold` : 스프링이 정지 상태로 간주되어야하는 정지 위치에서의 변위 임계 값입니다. 기본값은 0.001입니다.
- `restSpeedThreshold` : 스프링이 정지 상태에서 초당 픽셀로 간주되는 속도. 기본값은 0.001입니다.
- `delay` : 지연 후 애니메이션을 시작합니다 (밀리 초). 기본값은 0입니다.
- `isInteraction` :이 애니메이션이 InteractionManager에서 "상호 작용 핸들"을 작성하는지 여부 기본값은 true입니다.
- `useNativeDriver` : Uses the native driver when true. Default false.

&nbsp;

### add()

```javascript
static add(a, b)
```

함께 추가 된 두 개의 Animated 값으로 구성된 새로운 Animated 값을 만듭니다.

&nbsp;

### subtract()

```javas
static subtract(a, b)
```

첫 번째 Animated 값에서 두 번째 Animated 값을 빼서 구성된 새로운 Animated 값을 만듭니다.

&nbsp;

### **divide()**

```javas
static divide(a, b)
```

첫 번째 Animated 값을 두 번째 Animated 값으로 나누어 구성된 새로운 Animated 값을 만듭니다.

&nbsp;

### **multiply()**

```javascript
static multiply(a, b)
```

두 개의 Animated 값을 곱한 새 Animated 값을 만듭니다.

&nbsp;

### **modulo()**

```javascript
static modulo(a, modulus)
```

제공된 Animated 값의 (음수가 아닌) 모듈로 인 새로운 Animated 값을 만듭니다.

&nbsp;

### **diffClamp()**

```javascri
static diffClamp(a, min, max)
```

2 개의 값으로 제한된 새 애니메이션 값을 만듭니다. 마지막 값의 차이를 사용하므로 값이 범위를 벗어나더라도 값이 다시 가까워지면 변경을 시작합니다.

(`value = clamp(value + diff, min, max)`).

예를 들어 스크롤 할 때 탐색 표시 줄을 표시하고 아래로 스크롤 할 때 탐색 표시 줄을 숨기는 등의 스크롤 이벤트에 유용합니다.

&nbsp;

### **delay()**

```javascript
static delay(time)
```

주어진 지연 후에 애니메이션을 시작합니다.

&nbsp;

### **sequence()**

```javascript
static sequence(animations)
```

다음 배열을 시작하기 전에 각 배열이 완료 될 때까지 순서대로 애니메이션 배열을 시작합니다. 현재 실행중인 애니메이션이 중지되면 다음 애니메이션이 시작되지 않습니다.

&nbsp;

### **parallel()**

```javascript
static parallel(animations, config?)
```

동시에 애니메이션 배열을 시작합니다. 기본적으로 애니메이션 중 하나가 중지되면 모두 중지됩니다.

 `stopTogether` 플래그로 이를 대체 할 수 있습니다.

&nbsp;

### **stagger()**

```javascript
static stagger(time, animations)
```

애니메이션 배열은 병렬로 (오버랩) 실행될 수 있지만 연속적인 지연으로 순차적으로 시작됩니다. 후행 효과를 수행하기에 좋습니다.

&nbsp;

### **loop()**

```javas
static loop(animation, config?)
```

주어진 애니메이션을 계속 반복하여 애니메이션이 끝에 도달 할 때마다 재설정되고 처음부터 다시 시작됩니다. 

자식 애니메이션이 `useNativeDriver : true`로 설정된 경우 UI 스레드를 차단하지 않고 반복됩니다.

또한 루프를 사용하면 애니메이션이 실행되는 동안 `VirtualizedList` 기반 컴포넌트가 더 많은 행을 렌더링하지 못할 수 있습니다.

하위 애니메이션 구성에서 `isInteraction : false`를 전달하여이 문제를 해결할 수 있습니다.

구성은 다음 옵션이있을 수있는 개체입니다.

- `iterations(반복)` : 애니메이션이 반복되어야하는 횟수입니다. 기본값은 `-1` (무한)입니다.

&nbsp;

### **event()**

```javascript
static event(argMapping, config?)
```

매핑 배열을 가져 와서 각 인수에서 값을 추출한 다음 매핑 된 출력에서 setValue를 호출합니다. 예 :

```javascript
 onScroll={Animated.event(
   [{nativeEvent: {contentOffset: {x: this._scrollX}}}],
   {listener: (event) => console.log(event)}, // Optional async listener
 )}
 ...
 onPanResponderMove: Animated.event([
   null,                // raw event arg ignored
   {dx: this._panX}],    // gestureState arg
{listener: (event, gestureState) => console.log(event, gestureState)}, // Optional async listener
 ),
```

Config는 다음 옵션을 있을 수 있는 오브젝트입니다.

- `listener` : 선택적 비동기 리스너
- `useNativeDriver : true` 인 경우 기본 드라이버를 사용합니다. 기본값은 false입니다.

&nbsp;

### **forkEvent()**

```javascript
static forkEvent(event, listener)
```

props를 통해 전달되는 애니메이션 이벤트의 스누핑을 위한 고급 명령 API. 기존 `AnimatedEvent`에 새로운 자바 스크립트 리스너를 추가 할 수 있습니다. `animatedEvent`가 Javascript 리스너 인 경우 2 개의 리스너를 단일 리스너로 병합하고 `animatedEvent`가 null/undefined 경우 Javascript 리스너를 직접 지정합니다. 가능한 경우 직접 값(values directly)을 사용하십시오.

&nbsp;

### **unforkEvent()**

```javascript
static unforkEvent(event, listener)
```

&nbsp;

## **Properties**

### Value

애니메이션 구동을위한 표준 값 클래스. 

일반적으로 `new Animated.Value(0);`

&nbsp;

### **ValueXY**

팬 제스처와 같은 2D 애니메이션을 구동하기위한 2D 값 클래스.

&nbsp;

### **Interpolation**

flow에서 보간 유형을 사용하기 위해 내보냈습니다.

&nbsp;

### **Node**

간편한 유형 검사를 위해 내 보냅니다. 

모든 애니메이션 값은 이 클래스에서 파생됩니다.

&nbsp;

### **createAnimatedComponent**

React 컴포넌트를 애니메이션 가능하게 만듭니다. 

`Animated.View` 등을 만드는 데 사용됩니다.

&nbsp;

### **attachNativeEvent**

뷰의 이벤트에 애니메이션 값을 첨부하는 명령형(Imperative) API. 

가능한 경우 `useNativeDrive : true`와 함께 `Animated.event`를 사용하는 것이 좋습니다.

