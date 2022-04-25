## 1부 좋은 코드
### 1장 안정성
'어떻게 코드를 잘 작성해야 하는가?' 에 대한 견해를 밝히는 여러 책들은 변수, 클래스의 역할을 명확하게 하라고 조언하는데, 이는 가독성을 높이기도 하지만 안전한 프로그램을 만들 수 있는 방법이기 떄문이다. '안정성'은 개발에서 그 어떤 요소보다 가장 중요하게 생각하고 고려해야할 부분이 아닐까? 기본적이지만 가장 중요하기 때문인지 책에서 처음으로 소개하는 챕터는 '안정성' 에 관련된 내용이다. 코틀린은 '안전'하게 개발할 수 있는 최적의 언어이지만, 개발자가 주의깊게 사용하지 않으면 위험해지는 건 금방이다.

#### 아이템1. 가변성을 제한하라
[가변성을 왜 제한해야 하는가]
> 1. 프로그램을 이해하고 디버그하기 힘들어집니다.
> 2. 가변성이 있으면, 코드의 실행을 추론하기 어려워집니다.
> 3. 멀티스레드 프로그램일 때는 적절한 동기화가 필요합니다.
> 4. 테스트하기 어렵습니다.
> 5. 상태 변경이 일어날 때, 이러한 변경을 다른 부분에 알려야 하는 경우가 있습니다.

 여기에서 상태란 'var' 를 사용해 정의하거나 mutable 객체를 사용해 변경할 수 있는 요소를 말한다. 상태를 가지면 막상 사용하는 시점에 이 상태가 어떤 값을 가지고 있는지 알 수 없어 이를 판단하기 위해 변경했던 부분을 tracking 해야한다. 멀티스레드를 활용해 수정하는 경우는 특정 시점에 어떤 값을 가지게 될 지 추측하기 더 어려워진다. 따라서 변경 가능한 변수를 정의하는 것은 지양하는 것이 좋으며, 꼭 필요한 경우에만 사용해야한다.
 
[코틀린에서 가변성 제한하기]

immutable 한 변수를 보면 얼마나 마음이 편해지는가. 가변성을 제한하면 언제 값이 바뀌었는지 고민할 필요없이 항상 예측이 가능하다는 장점이 있다.

> * 읽기 전용 프로퍼티(val)
> * 가변 컬렉션과 읽기 전용 컬렉션 구분하기
> * 데이터 클래스의 copy

가변성을 제한하는 방법은 위와 같으나, 값이 정말 불변한다는 의미는 아니다. val 로 정의한다 한들 mutableList 로 mutableList 로 초기화하면 변경이 가능하다.

* 읽기 전용 프로퍼티(val)
```
val list = mutableListOf(1, 2, 3)

var name: String? = "Billie"
var surname: String? = "Eilish"
val fullName: String?
    get() = "$name $surname"
```
val 로 선언하더라도 list 처럼 mutable 한 객체라면 변할 수 있고, name, surname 에 의존적인 fullName 은 get() 시점에 "Billie Eilish" 가 된다는 보장이 없다. 이 떄문에 `if (fullName != null) println(fullName.length)` 처럼 스마트 캐스팅하여 사용할 수도 없다. 

> val 은 읽기 전용 프로퍼티지만, 변경할 수 없음(불변, immutable)을 의미하는 것은 아니라는 것을 기억하기 바랍니다. ... 만약 완전히 변경할 필요가 없다면, final 프로퍼티를 사용하는 것이 좋습니다.

* 가변 컬렉션과 읽기 전용 컬렉션 구분하기

![safety_item1_collection.png](collection.png)

Iterable, Collection, Set, List 은 읽기 전용 interface 이고, MutableIterable, MutableCollection, MutableSet, MutableList 은 읽고 쓸 수 있는 Collection 이다. Iterable<T>.map, Iterable<T>.filter 등은 ArrayList 를 return 하므로 내부 값을 변경할 수 없다는 것은 아니지만, interface 에서 변경 가능 함수를 제공하는 것은 아니다. 이는 외부적으로 immutable 하게 보이므로 안정성을 얻을 수 있다.
주의해야 할 점은 List 로 선언한 변수 list 를 `if (list is MutableList) list.add(4)` 처럼 다운캐스팅을 하면 안된다는 것이다. immutable 한 collection 사용 계약을 위반하고, 어느 누구도 이와 같은 코드가 중간에 들어갈 거라고 예상하지 못했을 것이다. 필요하다면 `list.toMutableList()` 를 활용해 copy 한 list 를 사용해야 한다. 

* 데이터 클래스의 copy

'얕은 복사'는 복사 대상의 '주소' 를 참조하고 있으므로 원본 객체와 복사 객체는 동일한 객체라고 볼 수 있다. 만약 얕게 복사한 객체를 수정하면 원본 객체를 사용하는 곳에서 예상치 못한 결과가 발생할 수 있다. 따라서 얕은 복사를 지양하고, 같은 데이터를 가진 객체가 필요하다면 '깊은 복사' 를 해야한다.
코틀린의 data class 를 사용하면 '깊은 복사'를 하는 `copy` 메서드를 만들어 주는데, 생성자 property 값이 동일한 새로운 객체를 만들어 준다. 특정 property 만 다르게 set 하고 싶다면 아래와 같이 사용할 수 있다. (참고로 지역 변수는 copy 대상에 포함되지 않는다.)
```
data class Profile(
    val name: String,
    val company: String,
    val department: String,
    val position: String
)

val profile = Profile("Billie", "Google", "Marketing", "Staff")
val newProfile = profile.copy(name = "Eilish")
```

[다른 종류의 변경 가능 지점]

변경 가능한 리스트를 정의해야 한다면 mutable 컬렉션을 사용할 지, var 로 property 를 정의할 지 고민하지 말고, 후자를 택하는 것이 좋다. 
```
val list1: MutableList<String> = mutableListOf()
var list2: List<String> = listOf()
```
mutable 리스트를 사용하는 list1 은 add, update, delete 가 가능하다. 멀티스레드를 사용할 경우 적절하게 동기화가 되도록 고려해야한다. 읽기 전용 컬렉션으로 정의된 list2 는 그 측면에서 조금 더 안정적이라고 할 수 있다. 더불어 setter() 를 활용해 추적이 가능하고, private 으로 만들 수 있어 객체 변경을 제어하기가 더 쉽다.

[변경 가능 지점 노출하지 말기]

```
val list: MutableList<String> = mutableListOf()
fun get(): MutableList<String> = list.copy()
```

```
val storedUsers: MutableMap<Int, String> = mutableMapOf()
fun get(): Map<Int, String> = storedUsers
```

mutable 객체를 클래스 외부로 노출하는 것은 변경 가능한 지점을 무한히 확대하는 방법이므로 노출하지 않는 것이 좋다. 필요하다면 copy 하여 return 하거나(방어적 복제), 읽기 전용 타입으로 업캐스트 하여 가변성을 제한할 수 있다.


#### 아이템2. 변수의 스코프를 최소화하라
변수는 사용 시점 직전에 정의하고, 가능하면 정의할 때 초기화 하는 것이 좋다. 정의 시점과 사용 시점이 멀어질수록 그 변수가 언제 어떻게 사용되었는지 이해하기 위한 과정이 필요하다. 더불어 다른 개발자에 의해 잘못 사용될 수 있으며, 점점 유지/보수가 어려운 코드가 될 가능성이 높다. 따라서 클래스 멤버변수는 꼭 필요한 경우에만 정의해야 하며, static 변수를 사용하는 건 더욱 지양해야 한다.

#### 아이템3. 최대한 플랫폼 타입을 사용하지 말라
코틀린은 nullable 여부를 명확히 표기해야 하므로 non-nullable 한 객체를 사용할 경우 관리하기 쉽고 이해하기도 쉬워진다. Java 를 사용할 때에 비해 Null-Pointer Exception(NPE) 발생 우려도 현저히 줄어들었다. 하지만 아직 Java 와 혼용해 사용하고 있다면 '플랫폼 타입' 을 알고 있어야 한다.
'플랫폼 타입' 은 자바 등의 다른 언어에서 넘어와 nullable 여부를 판단할 수 없는 타입을 말하며, `String!` 와 같이 타입 이름 뒤에 `!` 를 붙여 표기한다.
플랫폼 타입은 nullable 여부를 판단할 수 없기 때문에 언제 NPE 가 발생할지 예측할 수 없다. 따라서 Java 코드 조작이 가능하다면 `@Nullable` 혹은 `@NotNull` 어노테이션을 붙여 사용하고, `val userName: String = User().name` 혹은 `val userName: String? = User().name` 처럼 Java 객체(User)를 참조할 경우 타입을 명확히 해야한다. 코드가 더 비대해졌을 때 문제가 발생한다면 원인 파악이 더 어려워질텐데, non-nullable 한 변수로 정의하고 NPE 를 발생시켜 미리 문제를 발견하고 해결하는 것도 좋은 방법라고 생각한다.

#### 아이템4. inferred 타입으로 리턴하지 말라
타입 추론(type inference)은 `val profile = Profile()` 과 같이 타입을 명시하지 않더라도 할당된 데이터(`Profile`) 형태로 변수가 어떤 타입인지 추론하는 것이다.
> 리턴 타입은 API 를 잘 모르는 사람에게 전달해 줄 수 있는 중요한 정보입니다. 따라서 리턴 타입은 외부에서 확인할 수 있게 명시적으로 지정해 주는 것이 좋습니다.

안전을 위해 타입을 지정해야 한다는 부분에 공감한다. 대부분의 변수, 함수의 return 타입을 명시하는 것을 선호하나, `profile` 과 같이 변수명과 타입이 같거나 `var number = 0` 과 같이 어떤 개발자가 보아도 타입 추론이 가능한 '변수명' 으로 정의하는 경우에 한해서는 타입을 꼭 명시할 필요는 없다고 생각한다. 

### 아이템5. 예외를 활용해 코드에 제한을 걸어라
`require 블록`, `check 블록`, `assert 블록`, `return 또는 throw 와 함께 활용하는 Elvis 연산자` 를 사용하면 기대치 못한 동작을 하지 않도록 예방하고, 가독성을 높일 수 있다.

[아규먼트]
```
fun sendEmail(user: User, message: String) {
    requireNotNull(user.email)
    require(isValidEmail(user.email)) {  "User email is not valid"  }
}
```

`require` 를 사용하면 **함수의 argument** 의 제한을 확인하고, 만족하지 않을 경우 IllegalArgumentException 을 발생시킬 수 있다. (람다를 활용한 메시지 정의 가능) 위 예와 같이 로그인 UseCase 내 정의된 함수에서 email 의 유효성을 검사할 수 있다.

[상태]
```
fun update(profile: Profile?) {
    requireNotNull(profile)
    check(isInitialized)
}
```

어떤 객체가 사용 가능한 시점에 호출되어야 하는 함수이거나, 화면이 그려졌을 때 사용해야 하는 함수로 제한해야 하는 경우, `check` 를 사용해 확인하며 만족하지 않을 때 IllegalArgumentException 을 발생시킬 수 있다. `check` 는 `require` 와 비슷하나, '상태' 가 올바른지 확인할 때 사용한다. `require` 와 함께 사용하게 된다면 `require` 블록 뒤에 `check` 블록을 배치한다. 만약 

[Assert 계열 함수 사용]

UnitTest 를 활용해 구현한 내용이 정상적으로 동작 하는지 확인하는데, 이 때 `assert` 를 활용한다.

[nullability 와 스마트 캐스팅]

```
fun handleOuput(output: Output) {
    require(output is Output.Success)
    val success: Output.Success = output
    ...    
}
```
`require` 혹은 `check` 를 사용하면 스마트 캐스팅을 지원하므로 변수를 unpack 하는 용도로 활용할 수 있다.

```
fun sendEmail(user: User) {
    val email: String = user.email ?: run {
        log("email is not a valid format.")
        return
    } 
}
```
exception 을 발생시키면 문제가 있는 코드 위치를 바로 알 수 있지만, 사용성을 헤치는 일이므로 꼭 사용해야 하는 경우가 아니면 사용하지 않는다. 따라서 위 처럼 함수 시작 부분에서 return 혹은 throw 를 활용해 nullable 여부를 판단하는 경우도 많다. 함수 내에서 사용되는 변수가 null 이 아닐 때만 사용되어야 한다면 nullable 여부를 확인해 미리 return 하고 스마트 캐스팅된 변수를 활용한다. 


### 사용자 정의 오류보다는 표준 오류를 사용하라
Exception 을 발생시켜야 할 때, `IndexOutOfException`, `IllegalArgumentException` 과 같이 개발자가 이미 알고 있는 표준 라이브러리의 오류를 사용하는 것이 코드를 보는 다른 개발자들이 더 쉽게 이해할 수 있도록 돕는다.
