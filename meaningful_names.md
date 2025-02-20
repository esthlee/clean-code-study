# 2장 의미 있는 이름

### 의도가 분명하게 이름을 지으라

- 변수나 함수 그리고 클래스 이름은 다음과 같은 질문에 대한 답이 있어야한다.  
  - 변수(혹은 함수 또는 클래스)의 존재 이유는?
  - 수행 기능은?
  - 사용 방법은?

```python
# Bad
def c(a, b):
    return a + b

# Good
def calculate_total_price(item_price, tax):
    return item_price + tax
```

<br>

### 그릇된 단서를 피하라
  - 나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용해도 안된다.  
  - 예: 직각삼각형의 빗변을 규현할 때 `hp`가 약어로 보일지라도, `hp`라는 변수는 독자에게 그릇된 정보를 제공한다.

<br>

### 서로 흡사한 이름을 사용하지 않도록 주의한다.
  - 예: 여러 계정을 그룹으로 묶을 때, 실제 `List`가 아니라면, `accountList`라 명명하지 않는다. `List`는 특수한 의미기 때문.

<br>

### 유사한 개념은 유사한 표기법을 사용한다.
  - 예: 한 모듈에서 `XYZControllerForEfficientHandlingOfString` 사용하고, `XYZControllerForEfficientStorageOfString` 사용한다면, 차이를 보기 힘들다.

```python
# Bad
def load_user_data():
    pass

def fetch_user_information():
    pass

# Good
def load_user_data():
    pass

def load_user_preferences():
    pass

```

<br>

### 의미 있게 구분하라
  - 컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하는 것은 문제를 일으킨다.
  - 동일한 범위 안에서는 다른 두 개념에 같은 이름을 사용 못함. 그래서 프로그래머가 한쪽 이름을 마음대로 바꾼다면 오류가 생김.
  - 연속된 숫자를 덧붙이거나(변수 이름에 숫자를 추가하여 구분하는 방식, `data1`, `data2` 등) 불용어(`temp_`, `some_` )를 추가하는 방식은 적절하지 못하다.
  - 이름이 달라야 한다면, 의미도 달라질 것.
  - 연속적인 숫자를 덧붙인 이름(`a1`, `a2`…)은 아무런 정보 제공하지 않고, 저자의 의도가 드러나지 않기 때문에 부적절.
  - 함수 인수 이름으로 `source`와 `destination`을 사용한다면 코드 읽기가 쉬워진다.
  - 명확한 관례가 없다면, 변수 `moneyAmount`와 `money`는 구분이 안된다.  
    - **읽는 사람이 차이를 알도록 이름을 지어라.**

<br>

### 발음하기 쉬운 이름을 사용하라
  - 발음하기 쉬운 이름을 사용하면 토론하기 쉽다.

<br>

### 검색하기 쉬운 이름을 사용해라
  - 문자 하나를 사용하는 이름과 상수는 텍스트 코드에서 쉽게 눈에 띄지 않는다.
  - 이런 관점에서는 긴 이름이 짧은 이름보다 좋다.
  - 간단한 메서드에서 로컬 변수만 한 문자를 사용한다.
  - 이름 길이는 범위 크기에 비례한다.

<br>

### 인코딩을 피해라
  - 변수 이름에 타입을 인코딩할 필요가 없다.
    - 예: `PhoneNumber phoneString;` // 타입이 바뀌어도 이름은 바뀌지 않는다.

      ```python
      # Bad: 데이터 타입 기반 접두어
      strUserName = "John"   # str indicates a string
      intUserAge = 30        # int indicates an integer
      fltUserHeight = 5.9    # flt indicates a float

      # Good: Python은 변수의 타입을 명시할 필요가 없음
      user_name = "John"
      user_age = 30
      user_height = 5.9
      ```

  - 멤버 변수 접두어  
    - 멤버 변수에 `m_`이라는 접두어 붙일 필요 없다.  
    - 사람들은 접두어를 무시하고 이름을 해독하는 방법을 더 빨리 익힌다.
  - 인터페이스 클래스와 구현 클래스  
    - 인코딩이 필요한 경우도 있음.  
    - 예: 도형을 생성하는 `ABSTRACT FACTORY`를 구현한다고 가정했을 때, 이 팩토리는 인터페이스 클래스, 구현은 구체 클래스에서 한다.  
      - 내가 다루는 클래스가 인터페이스라는 사실을 남에게 알릴 필요 없다.
      - 인터페이스 이름에 접두어 I를 붙이면 주의를 흐트리고 과도한 정보를 제공한다.  
      - 인터페이스 클래스와 구현 클래스 이름 중 하나를 인코딩해야 한다면, 구현 클래스가 적합하다.  
        - 예: `ShapeFactoryImp`, `CShapeFactory`가 `IShapeFactory`보다 좋다.

<br>

### 자신의 기억력을 자랑하지 마라
- 변수 이름은 자신만 이해할 수 있는 이름이 아니라, 누구나 쉽게 이해할 수 있는 이름으로 작성한다.
- 반복문에서 사용하는 변수는 문자 하나만(`i`, `j`) 사용해도 괜찮지만, 그 외의 경우에는 명확한 의미를 담은 이름을 사용한다.

<br>

### 클래스 이름
- 클래스 이름과 객체 이름은 **명사**나 **명사구**를 사용한다.
  - 예: `Customer`, `Account`, `AddressParser`
- `Manager`, `Processor`, `Data`와 같이 모호한 이름은 피한다.

<br>

### 메서드 이름
- 메서드 이름은 **동사**나 **동사구**로 작성한다.
  - 예: `post_payment`, `delete_page`, `save_file`
- 생성자를 중복정의할 때는 정적 팩토리 메서드를 사용한다. 메서드는 인수를 설명하는 이름을 사용한다.

```python
class Complex:
    def __init__(self, real: float, imaginary: float):
        self.real = real
        self.imaginary = imaginary

    @staticmethod
    def from_real_number(real: float) -> 'Complex':
        """실수(real)로부터 복소수를 생성"""
        return Complex(real, 0.0)

    @staticmethod
    def from_imaginary_number(imaginary: float) -> 'Complex':
        """허수(imaginary)로부터 복소수를 생성"""
        return Complex(0.0, imaginary)

# 사용 예
fulcrum_point = Complex.from_real_number(23.0)
print(f"Real: {fulcrum_point.real}, Imaginary: {fulcrum_point.imaginary}")

# 아래와 같이 생성자를 직접 호출하는 것보다 명확함
alternative_point = Complex(23.0, 0.0)
```

- 정적 팩토리 메서드를 사용하는 이유
  - 메서드 이름을 통해 객체 생성의 의도가 더 분명하게 드러난다.
    - `Complex.from_real_number(23.0)`은 객체가 실수(real)를 기반으로 생성된다는 점을 명확히 한다.
  - 객체 생성 로직을 메서드 내부에 캡슐화할 수 있어 필요 시 객체 생성 방식을 변경하기 쉽다.

<br>

### 기발한 이름은 피한다
- 이름이 지나치게 창의적이면 이해하기 어렵다.
- 재미있는 이름보다 명확한 이름을 선택한다.

<br>

### 한 개념에 한 단어를 사용하라
- 추상적인 개념 하나에 단어 하나를 선택해 일관되게 작성한다.
  - 예: 데이터를 가져오는 메서드를 `fetch`, `retrieve`, `get` 등으로 혼용하지 않는다.

<br>

### 말장난을 하지 마라
- 하나의 단어를 두 가지 목적으로 사용하지 않는다.
- 다른 개념에 같은 단어를 사용하면 혼란을 줄 수 있다.
  - 예: `add`가 두 값을 더하거나 새로운 값을 생성하는 뜻으로 쓰였다면, 집합에 값을 추가하는 메서드는 `add`가 아닌 `insert`나 `append`를 사용해야 한다.

<br>

### 의미 있는 맥락을 추가하라
- 변수 이름이나 함수 이름에 맥락이 부족하면 의미를 알기 어렵다.
- 필요한 경우 접두어나 접미어를 사용해 맥락을 추가한다.
- 클래스를 생성하면 더 좋다. 변수가 좀 더 큰 개념에 속한다는 사실이 분명해진다.