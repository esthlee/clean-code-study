### 1. 자신의 기억력을 자랑하지 마라
- 변수 이름은 자신만 이해할 수 있는 이름이 아니라, 누구나 쉽게 이해할 수 있는 이름으로 작성한다.
- 반복문에서 사용하는 변수는 문자 하나만(`i`, `j`) 사용해도 괜찮지만, 그 외의 경우에는 명확한 의미를 담은 이름을 사용한다.

<br>

### 2. 클래스 이름
- 클래스 이름과 객체 이름은 **명사**나 **명사구**를 사용한다.
  - 예: `Customer`, `Account`, `AddressParser`
- `Manager`, `Processor`, `Data`와 같이 모호한 이름은 피한다.

<br>

### 3. 메서드 이름
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

### 4. 기발한 이름은 피한다
- 이름이 지나치게 창의적이면 이해하기 어렵다.
- 재미있는 이름보다 명확한 이름을 선택한다.

<br>

### 5. 한 개념에 한 단어를 사용하라
- 추상적인 개념 하나에 단어 하나를 선택해 일관되게 작성한다.
  - 예: 데이터를 가져오는 메서드를 `fetch`, `retrieve`, `get` 등으로 혼용하지 않는다.

<br>

### 6. 말장난을 하지 마라
- 하나의 단어를 두 가지 목적으로 사용하지 않는다.
- 다른 개념에 같은 단어를 사용하면 혼란을 줄 수 있다.
  - 예: `add`가 두 값을 더하거나 새로운 값을 생성하는 뜻으로 쓰였다면, 집합에 값을 추가하는 메서드는 `add`가 아닌 `insert`나 `append`를 사용해야 한다.

<br>

### 7. 의미 있는 맥락을 추가하라
- 변수 이름이나 함수 이름에 맥락이 부족하면 의미를 알기 어렵다.
- 필요한 경우 접두어나 접미어를 사용해 맥락을 추가한다.
- 클래스를 생성하면 더 좋다. 변수가 좀 더 큰 개념에 속한다는 사실이 분명해진다.
