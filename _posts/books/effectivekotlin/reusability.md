## 1부 좋은 코드
### 3장 재사용성
재사용 가능한 함수는 한 번 만들어 놓으면 필요할 때마다 만들 필요없이 가져다 사용할 수 있다. 다만 재사용 가능한 함수를 만들 것인지의 여부를 판단하는 것은 생각보다 까다롭다. 로직을 조금만 수정해도 사용하는 부분에 영향을 크게 미칠 수 있다.     

#### 아이템19. knowledge 를 반복하여 사용하지 말라
[knowledge]
넓은 의미로 의도적인 정보를 뜻한다. 프로젝트를 진행할 때 정의한 모든 것이 knowledge 이다.

프로그램에서 중요한 knowledge 를 뽑는다면 아래 두 가지가 있으며, 이는 시간에 따른 변화의 차이로 구분할 수 있다. 
1. 로직(logic): 프로그램이 어떤 식으로 동작하는지, 어떻게 보이는지. 시간이 지나면서 변하는 로직
2. 공통 알고리즘(common algorithm): 원하는 동작을 하기 위한 알고리즘. 한 번 정의된 후에 크게 변화지 않는 로직

[모든 것은 변화한다]
변화할 때 가장 큰 적은 knowledge 가 반복되어 있는 부분이다. knowledge 반복은 프로젝트의 확장성을 막고, 쉽게 깨지게 만든다.

[언제 코드를 반복해도 될까?]
얼핏보면 knowledge 반복처럼 보이지만, 실질적으로 다른 knowledge 를 나타내므로 추출하면 안된다. 신중하지 못한 추출은 변경을 더 어렵게 만든다.
'상속'은 신중하게 결정해야 하는데, 부모 클래스에서만 사용하고 자식 클래스에서는 사용하지 않는다면 자식 클래스 입장에서는 불필요한 함수를 노출하는 것이므로 다른 클래스라고 보는 게 맞다.
같은 디자인의 화면을 재사용 하는 것도 위험할 수 있는데, UI 는 변화 가능성이 높으므로, 디자인은 같아도 기능이 조금씩 달라지고 이는 점점 다른 화면이 될 가능성도 생긴다.
'공통' 과 '비슷' 은 엄연히 다르다. 같은 디자인인데 분기처리를 하나 이상 해야한다면 다른 클래스라고 보는 것이 좋다.

[단일 책임 원칙]
함수가 해야하는 일 이외의 책임을 가지고 있을 거라는 예측을하지 못한다면 이는 단일책임원칙을 지키지 않은 것이다.


### 아이템20. 일반적인 알고리즘을 반복해서 구현하지 말라
프로젝트에 국한된 로직이 아닌 수학적인 연산, 수집 처리처럼 별도로 분리할 수 있는 부분은 재사용 함수로 구현하는 것이 좋다.
함수의 이름 등만 보고 무엇을 하는지 확실하게 알 수 있고, 잘 알려진 수학적 개념을 정의한 함수는 public 한 함수로 구현하는 것이 불필요하게 반복되는 코드를 작성하는 것보다 낫다.
