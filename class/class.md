# 10장 클래스 

## 클래스 체계 
- 클래스 변수 (정적 공개 상수, 정적 변수)
- 정적 비공개 변수 (_ 또는 __ 사용_)
- 비공개 인스턴스 변수 (__ 사용)
- 공개 인스턴스 변수
```python
class Example:
    # 1. 정적 공개 상수 (Public Static Constant)
    MAX_COUNT = 100  # 모든 인스턴스가 공유하는 상수 (자바의 public static final)

    # 2. 정적 비공개 변수 (Private Static Variable)
    __hidden_value = "비공개 정적 변수"  # 클래스 내부에서만 사용 (자바의 private static)

    def __init__(self, value):
        # 3. 비공개 인스턴스 변수 (Private Instance Variable)
        self.__private_var = value  # 외부에서 직접 접근 불가 (자바의 private 필드)

        # 4. 공개 인스턴스 변수 (Public Instance Variable)
        self.public_var = "공개 변수"  # 누구나 접근 가능 (자바의 public 필드와 유사)

    # 비공개 변수 접근 메서드 (Getter & Setter)
    def get_private_var(self):
        return self.__private_var

    def set_private_var(self, value):
        self.__private_var = value

```
### 캡슐화
- 변수와 유틸리티 함수는 가능한 공개하지 않는 편이 낫지만 반드시 숨겨야 한다는 법칙도 없다.
- 변수나 유틸리티 함수를 protected 로 선언해 테스트 코드에 접근을 허용하기도 한다.
- 가능한 비공개 상태를 유지하고, 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.

## 클래스는 작아야 한다!
- 클래스 생성 첫번째 규칙: 작아야 한다.
- 클래스 이름은 해당 클래스 책임을 기술해야 한다.
예. Processor, Manager, Super 등과 같이 모호한 단어가 있다면, 클래스에 여러 책임을 떠안긴 경우
- 클래스 설명은 if, and, -, but 을 사용하지 않고서 25 단어 내외로 가능해야한다. 

### 단일 책임 원칙 
- 이는 클래스나 모듈을 변경할 이유가 단, 하나뿐이어야 한다는 원칙.
- 책임, 즉 변경할 이유를 파악하려 애쓰다 보면 코드를 추상화하기 쉬워진다.
- 큼직한 다목적 클래스 몇 개로 이루어진 시스템은 변경을 가할때, 당장 알 필요 없는 사실까지 알려줘 독자를 방해한다.
- 큰 클래스 몇 개가 아니라 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다.
- 작은 클래스는 각자 맡은 책임이 하나며, 변경할 이유가 하나며, 다른 작은 클래스와 협력해 시스템을 필요한 동작을 수행한다.
  ```python
  class Engine:
    def start(self):
        return "엔진 가동"

class Transmission:
    def shift_gear(self):
        return "기어 변경"

class Car:
    def __init__(self, brand):
        self.brand = brand
        self.engine = Engine()
        self.transmission = Transmission()

    def drive(self):
        return f"{self.brand} 주행 중 → {self.engine.start()} → {self.transmission.shift_gear()}"

# 사용 예
car = Car("Hyundai")
print(car.drive())```

### 응집도
- '함수를 작게, 매게변수 목록을 짧게' 라는 전략을 따르다 보면 때때로 몇몇 메서드만이 사용하는 인스턴스 변수가 아주 많아진다.
- 이는 새로운 클래스로 쪼개야한다는 신호. 응집도가 높아지도록 변수와 메서드를 적절히 분리해 새로운 클래스 두세개로 쪼개준다.

### 응집도를 유지하면 작은 클래스 여럿이 나온다. 

