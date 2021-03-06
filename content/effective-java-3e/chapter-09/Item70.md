# [아이템 70] 복구할 수 있는 상황에서는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 시용하라.

자바에서는 기본적으로 오류는 `checked exception`, `runtime exception`, `error` 이렇게 세 가지로 나눌 수 있습니다. 100% 확실한 건 아니지만 일반적으로 사용하는 상황은 다음과 같습니다.
</br>
</br>
호출 하는 쪽에서 복구하리라 여거지는 상황에서는 검사 예외를 사용합시다. 이것이 검사 예외랑 비검사 예외를 구분하는 가장 기본적인 규칙입니다. catch로 잡아서 처리하거나 thorws로 예외를 호출한쪽으로 전파하고 호출했을 때 발생할 수 있는 있다는 걸 API 사용자에게 알리는 것입니다.

</br>
</br>

반면 비검사 예외는 잡을 필요가 없습니다. 프로그램에서 비검사 예외를 던졌다는 것은 복구가 불가능 하거나 더 실행하면 득보다 실이 많다는 뜻입니다. 런타임 예외는 프로그래밍 오류를 나타낼떼 사용하는 게 좋습니다. 이러한 런타임 예외는 대부분 제약 조건에 부합하지 않을 때 사용되며 복구가 가능한지 불가능한지 명확히 구분되기 쉽지 않습니다. 만약 확신이 들지 않을 경우는 비검사 예외를 선택하는 게 좋을 것입니다.

</br>
</br>

에러는 보통 JVM 자원 부족 등 상황에서 사용합니다. 규약상 Error를 상속해 하위 클래스를 만드는 일은 좋지 않습니다. 비검사 예외를 구현할 때는 모두 RuntionException의 하위클래스 이어야 합니다.