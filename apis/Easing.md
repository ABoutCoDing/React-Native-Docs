# **Easing**

> React Native 의 [APIs > Easing](https://facebook.github.io/react-native/docs/easing) 의 번역 문서입니다. 번역 오류는 댓글로 남겨 주세요. 원문을 보시기를 추천드립니다.

`Easing` 모듈은 일반적인 easing functions를 구현합니다. 이 모듈은 [Animated.timing()](https://facebook.github.io/react-native/docs/animated#timing)에서 애니메이션에서 물리적으로 믿을 수있는 motion을 전달하는 데 사용됩니다.

http://easings.net/에서 일반적인 여유 기능에 대한 시각화를 찾을 수 있습니다.

&nbsp;

### **사전 정의된 애니메이션 (Predefined animations)**

`Easing` 모듈은 다음 방법을 통해 미리 정의 된 여러 애니메이션을 제공합니다.

- [back](#back)은 뒤로 이동하면 객체가 조금 뒤로 이동하는 기본 애니메이션을 제공합니다.
- [bounce](#bounce) 는 튀는(bounce) 애니메이션을 제공합니다
- [ease](#ease)는 기본 관성(inertial) 애니메이션을 제공합니다
- [elastic](#elastic)은 기본적인 스프링 상호 작용(interaction)을 제공합니다

&nbsp;

### **표준 함수 (Standard functions)**

세 가지 easing functions이 제공됩니다.

- [linear](#linear)
- [quad](#quad)
- [cubic](#cubic)

[poly](#poly) 함수는 quartic, quintic 및 기타 higher power 함수를 구현하는 데 사용할 수 있습니다.

&nbsp;

### **추가 기능 (Additional functions)**

다음과 같은 방법으로 추가 수학 함수가 제공됩니다.

- [bezier](#bezier) 는 3 차 bezier 곡선을 제공합니다
- [circle](#circle) 은 원형 함수(circular function)를 제공합니다
- [sin](#sin) 은 정현파 함수(sinusoidal function)를 제공합니다
- [exp](#exp) 는 지수 함수를 제공합니다

다음 헬퍼는 다른 easing function을 수정하는 데 사용됩니다.

- [in](#in) 은 easing function을 앞으로 실행
- [inOut](#inout) 은 easing function를 대칭으로 만듭니다.
- [out](#out) 은 easing function을 뒤로 실행합니다

&nbsp;

# **Reference**

## Methods

### step0()

```javascript
static step0(n)
```

스테핑 함수는 `n`의 양수 값에 대해 1을 반환합니다.

&nbsp;

### step1()

```javascript
static linear(t)
```

스테핑 함수는 `n`이 1보다 크거나 같으면 1을 반환합니다.

&nbsp;

### **linear()**

```javascript
static linear(t)
```

선형 함수, `f(t) = t`. 위치는 일대일 경과 시간과 관련됩니다.

http://cubic-bezier.com/#0,0,1,1

&nbsp;

#### **ease()**

```javascript
static ease(t)
```

물체가 속도로 천천히 가속되는 것과 유사한 기본 관성 상호 작용(interaction).

http://cubic-bezier.com/#.42,0,1,1

&nbsp;

### **quad()**

```javascript
static quad(t)
```

이차 함수 `f(t) = t * t`. 위치는 경과 시간의 제곱과 같습니다.

http://easings.net/#easeInQuad

&nbsp;

### **cubic()**

```javascript
static cubic(t)
```

입방(cubic) 함수, `f(t) = t * t * t`. 위치는 경과 시간의 큐브(cube)와 같습니다.

http://easings.net/#easeInCubic

&nbsp;

### **poly()**

```javascript
static poly(n)
```

A power function. 위치는 경과 시간의 N번(Nth) 거듭 제곱과 같습니다.

n = 4: http://easings.net/#easeInQuart n = 5: http://easings.net/#easeInQuint

&nbsp;

### **sin()**

```javascript
static sin(t)
```

 sinusoidal function (정현파 함수)

http://easings.net/#easeInSine

&nbsp;

### **circle()**

```javascript
static circle(t)
```

A circular function (원형 함수)

http://easings.net/#easeInCirc

&nbsp;

### **exp()**

```javascript
static elastic(bounciness)
```

An exponential function. (지수 함수)

http://easings.net/#easeInExpo

&nbsp;

### **elastic()**

```javascript
static elastic(bounciness)
```

스프링이 앞뒤로 진동하는 것과 유사한 elastic 상호작용(interaction),

기본 바운스는 1이며, 약간(little bit) 한 번 초과(overshoots)합니다. 0 바운스(bounciness)는 전혀 초과(overshoots)하지 않으며 N > 1의 바운스는 약 N회 초과(overshoot) 합니다.

http://easings.net/#easeInElastic

&nbsp;

### **back()**

```javascript
static back(s)
```

`Animated.parallel()`과 함께 사용하여 애니메이션이 시작될 때 객체가 조금 애니메이션되는 기본 효과를 만듭니다.

&nbsp;

### **bounce()**

```javascript
static bounce(t)
```

기본  bouncing effect를 제공합니다.

http://easings.net/#easeInBounce

&nbsp;

### **bezier()**

```javascript
static bezier(x1, y1, x2, y2)
```

CSS Transitions의 transition-timing-function에 해당하는 3 차 bezier 곡선을 제공합니다.

3차 bezier 곡선을 시각화하는 유용한 도구는 http://cubic-bezier.com/에서 찾을 수 있습니다.

&nbsp;

### **in()**

```javascript
static in easing;
```

easing function을 앞으로 실행합니다.

&nbsp;

### **out()**

```javascript
static out(easing)
```

easing function을 뒤로 실행합니다.

&nbsp;

### **inOut()**

```javascript
static inOut(easing)
```

여유 기능을 대칭으로 만듭니다. easing function는 지속 시간의 절반 동안 앞으로 실행 된 다음 나머지 지속 시간 동안 뒤로 실행됩니다.

