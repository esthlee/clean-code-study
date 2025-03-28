# 5장 형식 맞추기

가독성과 스타일은 유지보수 용이성과 확장성에 영향을 미치므로 코드 형식은 매우 중요하다.

## 적절한 행 길이를 유지하라

일반적으로 큰 파일보다 작은 파일이 이해하기 쉽습니다. **되도록이면 200줄 안에서 구현**할 수 있도록 작성하라.

### 신문 기사처럼 작성하라

- **요약 먼저**: 최상단에 요약 섹션을 추가하여 전체 내용을 몇 마디로 간단히 설명하라.
- **전체 그림부터 세부 사항으로**:
  - 첫 문단은 큰 그림을 보여주고, 아래로 내려갈수록 세부 사항이 드러난다.
- **명확한 이름**: 이름만 보고도 해당 모듈의 의도를 파악할 수 있을 정도로 명확하게 지어야 한다.
- **구조적 설명**:
  - 소스 파일의 상단은 고차원 개념과 알고리즘을 설명한다.
  - 아래로 내려갈수록 세부적인 의도를 묘사하며, 마지막에는 저차원 함수와 구현 세부 사항을 기술한다.

## 개념은 빈 행으로 분리하라

빈 행은 새로운 개념을 시작한다는 **시각적 단서** 이고, 가독성을 높여준다.
```python
data = [1, 2, 3, 4, 5]
transformed_data = [x * 2 for x in data]

filtered_data = [x for x in transformed_data if x > 5]

print("Original Data:", data)
print("Transformed Data:", transformed_data)
print("Filtered Data:", filtered_data)
```

### 세로 밀집도

- **줄바꿈은 적당히**: 너무 많이 줄바꿈하면 가독성이 떨어진다.
```python
# Bad example 
data = [1, 2, 3, 4, 5]



transformed_data = [x * 2 for x in data]
```

### 수직 거리

- **밀접한 개념은 가까이 배치**:
  1. **변수 선언**: 사용하는 위치에 최대한 가까이 선언합니다. 루프를 제어하는 변수는 루프 내부에 선언한다.
```python
# 잘못된 예: 루프 제어 변수를 루프 외부에서 선언
index = 0  # 루프 제어 변수가 루프와 멀리 떨어져 있음
for value in data:
    print(f"Index {index}: Value {value}")
    index += 1

# 개선된 예: 루프 내부에서 제어 변수 선언
for index, value in enumerate(data):  # 제어 변수를 enumerate로 루프 안에서 생성
    print(f"Index {index}: Value {value}")
```

  2. **인스턴스 변수**: 클래스 맨 처음에 선언하며, 변수 간에 세로로 거리를 두지 않는다.
```python
class Person:
    def __init__(self, name, age, city):
        self.name = name  # 인스턴스 변수 선언
        self.age = age    # 인스턴스 변수 선언
        self.city = city  # 인스턴스 변수 선언

    def introduce(self):
        print(f"My name is {self.name}, I am {self.age} years old, and I live in {self.city}.")
```

  3. **종속 함수**: 호출하는 함수는 호출되는 함수보다 위에 배치합니다. 이는 독자가 호출된 함수를 바로 찾을 수 있도록 도와준다.
```python
def calculate_area(radius):
    return 3.14 * radius ** 2

def print_circle_area(radius):
    area = calculate_area(radius)  
    print(f"The area of the circle with radius {radius} is {area}")

print_circle_area(5)
```

  4. **개념적 유사성**: 친화도가 높은 코드는 가까이 배치한다.
     - 예: 호출 관계가 있는 함수, 변수와 이를 사용하는 함수, 유사한 동작을 수행하는 함수.

### 세로 순서

- 소스 코드 모듈은 **고차원에서 저차원**으로 내려간다.
  - 중요한 개념을 먼저 기술하고, 세세한 사항은 마지막에 기술.
  ```python
  class DataProcessor:

    def process_data(self, data):
        cleaned_data = self._clean_data(data) 
        transformed_data = self._transform_data(cleaned_data)  
        return transformed_data

    def _clean_data(self, data):
        return [item.strip() for item in data if isinstance(item, str)]

    def _transform_data(self, data):
        return [item.upper() for item in data]```
  
## 가로 형식 맞추기

- **행 길이**: 80자에서 120자 사이로 작성하라.

### 가로 공백과 밀집도

- **공백을 활용하여 밀집한 개념과 느슨한 개념을 구분**:
  - 할당연산자를 강조하려고 앞뒤 공백을 준다. 할당문은 왼쪽 요소와 오른쪽 요소가 분명히 나뉜다.
    ```python
    numbers    = [1, 2, 3, 4, 5]
    person     = {"name": "Bob", "age": 30}
    is_active  = True
    ```
  - 함수 이름과 이어지는 괄호 사이에는 공백을 넣지 않는다. 함수와 인수는 서로 밀접하기 때문이다. (파이썬 PEP 8 스타일 가이드) 
    ```python
    def greet(name):
    return f"Hello, {name}!"

    message = greet("Alice")
    print(message)```

### 가로 정렬

- **불필요한 정렬은 피하라**: 정렬이 필요할 정도로 목록이 길다면 목록을 나누거나 줄여라.

### 들여쓰기

- **계층 표현**: 코드의 계층을 들여쓰기로 표현한다.
- **들여쓰기 규칙 준수**:
  - 짧은 `if` 문, `while` 문, 짧은 함수에서도 들여쓰기 규칙을 반드시 준수한다.

### 가짜 범위

- 빈 `while` 문이나 `for` 문은 세미콜론(`;`)을 새 행에 들여쓰기하여 추가한다.
```python
while some_condition:
    ;
```

## 팀 규칙

- **구현 스타일 논의**: 팀원 간에 스타일을 일관되게 유지한다.
- **스타일 통일**: 온갖 스타일을 뒤섞어 소스 코드를 복잡하게 만드는 실수를 피하라.
