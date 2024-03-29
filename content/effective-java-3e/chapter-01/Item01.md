---
title: "[아이템 1] 생성자 대신 정적 팩토리 메서드를 고려하라"
date: 2020-06-18T15:49:20+09:00
tags: ["Java", "Effective Java 3E"]
categories: ["Effective Java 3E"]
series: ["Effective Java 3E"]
chapter: ["Effective Java 3E Chapter 01"]
author: ["Qutrits"]
showToc: true
showAsideToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
showReadingTime: true
showPostNavLinks: true
showCodeCopyButtons: true
ShowBreadCrumbs: true
showContentProgressbar: true
# cover:
#   hidden: true
#   image: "/logo/logo-effective-java-3e.png"
---
## 일반적으로 사용하는 public 생성자 대신, 별도로 정적 팩토리 메서드를 이용할 수 있다.
<br>

``` java
public static Boolean valueOf(boolean b) {
    return b ? Boolean.TRUE : Booelan.FALSE;
}
```
_Boolean 클래스에서 발췌한 예제 코드_
<br>

## <i class="user-fa-action-done" aria-hidden="true"></i> 장점

### {{< font color-var="main-color" text="첫" >}} 번째, 이름을 가질 수 있다.

생성자에 넘기는 매개변수만으로 반환될 객체의 특성을 제대로 설명하지 못합니다. 반면 정적 팩토리 메서드는 이름을 잘 지으면 반환될 객체의 특성을 쉽게 묘사할 수 있습니다.

하나의 시그니처로는 생성자를 하나만 만들 수 있으며 입력 매개변수들의 순서를 다르게 한 생성자를 추가하는 식으로 이 제한을 피할 수는 있지만, 이 역시 객체의 특성을 살리기 힘들고 좋지 않은 방식입니다.

정적 팩토리 메서드는 이러한 제약이 없으며 시그니처가 같은 생성자가 여러 개 필요하다 싶으면
생성자를 정적 팩토리 메서드로 바꾸고 각각의 차이를 잘 드러내는 이름을 지어내는 방법을 사용할 수 있습니다.
<br>
<br>

### {{< font color-var="main-color" text="두" >}} 번째, 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

불변 클래스`immutable class`는 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있습니다. 따라서 객체가 자주 요청되는 상황이라면 성능을 상당히 끌어 올릴 수 있습니다. `Boolean.valueOf(boolean)`메서드도 그 경우에 해당됩니다.
<br>
<br>

### {{< font color-var="main-color" text="세" >}} 번째, 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

반환할 객체의 클래스를 선택할 수 있는 유연함이 있습니다. 반환할 객체의 하위 타입의 인스턴스를 반환할 수 있습니다. API를 작성할 때 이러한 유연성을 이용하여 구현 클래스를 공개하지 않고도 그 객체를 반환할 수 있어 API를 작게 유지할 수 있습니다.

자바 컬렉션 프레임워크는 핵심 인터페이스들에 수정 불가나 동기화 등의 기능을 덧붙인 총 45개의 유틸리티 구현체를 제공하는데, 이 구현체는 대부분 단 하나의 인스턴스화 불가 클래스인`java.util.Collections`에서 정적 팩토리 메서드를 통해 얻도록 했습니다.

컬렉션 프레임워크는 이 45개의 클래스를 공개하지 않기 때문에 API의 외견을 훨씬 작게 만들 수 있었습니다.

여기서 API의 크기는 프로그래머가 API를 사용하기 위해 익혀야 하는 개념의 수와 난이도를 의미합니다. 클라이언트가 구현체가 아닌 인터페이스를 다루게 되므로 역할과 구현을 나눠 결합도를 낮출 수 있습니다.
<br>
<br>

### {{< font color-var="main-color" text="네" >}} 번째, 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

{{< font color-var="main-color" text="세 번째" >}}에서 말했듯이 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없습니다. 가령 `EnumSet`클래스는 `public`생성자 없이 오직 정적 펙토리 메서드만 제공합니다.
<br>
```java
public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
    Enum<?>[] universe = getUniverse(elementType);
    if (universe == null)
        throw new ClassCastException(elementType + " not an enum");

    if (universe.length <= 64)
        return new RegularEnumSet<>(elementType, universe);
    else
        return new JumboEnumSet<>(elementType, universe);
}
```

`noneOf` 메서드를 살펴보면 원소 수에 따라 `RegularEnumSet` 이나 `JumboEnumSet` 인스턴스를 반환합니다.
<br>
<br>

### {{< font color-var="main-color" text="다섯" >}} 번째, 정적 팩토리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
대표적인 프레임워크로는 `JDBC`가 있습니다. 구현체를 클라이언트에 제공하는 역할을 프레임워크가 통제하여, 클라이언트를 구현체로부터 분리해줍니다.

서비스 제공자 프레임워크는 3개의 핵심 컴포넌트로 이루어집니다. (제공자는 서비스의 구현체를 의미합니다)
- 서비스 인터페이스 ({{< font color-var="main-color" weight="600" text="구현체의 동작을 정의" >}})
- 제공자 등록 API  ({{< font color-var="main-color" weight="600" text="제공자가 구현체를 등록할 때 사용" >}})
- 서비스 접근 API  ({{< font color-var="main-color" weight="600" text="클라이언트가 서비스의 인스터스를 얻을 때 사용" >}})

서비스 접근 API를 사용할 때 원하는 구현체의 조건을 명시할 수 있습니다. 조건을 명시하지 않으면 기본 구현체를 반환하거나 지원하는 구현체들을 하나씩 돌아가면서 반환합니다.
<br>
<br>

## <i class="user-fa-action-done" aria-hidden="true"></i> 단점

### {{< font color-var="main-color" text="첫" >}} 번째, 상속을 하려면 public이나 protected생성자가 필요하기 때문에 정적 팩토리 메서드만 제공하면 하위 클래스를 만들 수 없다.
이 제약은 상속`is-a`보다 컴포지션`has-a`을 사용하도록 유도하고 불변 타입으로 만들려면 이 제약을 지켜야 한다는 점에서 오히려 장점으로 받아들일 수도 있습니다.
<br>
<br>

### {{< font color-var="main-color" text="두" >}} 번째, 정적 팩토리 메서드는 프로그래머가 찾기 어렵다.
생성자처럼 API설명에 명확히 드러나지 않으므로 사용자는 정적 팩토리 메서드 사용 방식 클래스를 인스턴스화 시킬 방법을 알아내야 합니다. 그러므로 API 문서를 잘 정리하고 메서드 이름도 널리 알려진 규약을 따르는 게 좋습니다.
<br>