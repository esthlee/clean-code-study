## Chapter 3: 함수 (Functions)

### 작게 만들어라 (Small!)

함수는 작아야 한다.  
한눈에 들어오도록 함수를 짧게 작성해야 한다. 코드가 짧아지면 가독성이 높아지고 이해하기 쉬워진다. 예를 들어, 아래 코드는 너무 길고 여러 가지 일을 수행하고 있다.

```python
def render_page_with_setup_and_teardown(page_data, include_suite_setup):
    buffer = []
    if page_data.has_attribute("Test"):
        if include_suite_setup:
            suite_setup = get_inherited_page("SuiteSetup", page_data)
            if suite_setup:
                buffer.append(f"!include -setup {suite_setup}\n")
        setup = get_inherited_page("Setup", page_data)
        if setup:
            buffer.append(f"!include -setup {setup}\n")
    buffer.append(page_data.get_content())
    return ''.join(buffer)
```

리팩토링을 통해 더 단순하게 만들 수 있다.

<br>

### 한 가지만 해라 (Do One Thing)

함수는 **하나의 일만 수행**해야 한다. 함수가 여러 일을 하게 되면 코드를 이해하기 어려워지고 유지 보수가 복잡해진다.

```python
def render_page_with_setups_and_teardowns(page_data, is_suite):
    if is_test_page(page_data):
        include_setup_and_teardown_pages(page_data, is_suite)
    return page_data.get_html()
```

이 함수는 다음과 같은 일을 한다:  
1. 페이지가 테스트 페이지인지 확인한다.  
2. 그렇다면 설정 페이지와 해제 페이지를 넣는다.  
3. 페이지를 HTML로 렌더링한다.

위에서 언급하는 세 단계는 지정된 함수 이름 아래 추상화 수준이 하나다. 지정된 함수 이름 아래 추상화 수준이 하나라면 그 함수는 한가지 작업만 한다.

<br>

### 함수 당 추상화 수준은 하나로 (One Level of Abstraction per Function)

함수 내의 모든 코드가 **동일한 추상화 수준**을 유지해야 한다. 높은 수준의 작업과 세부적인 작업이 섞여 있으면 읽기 어렵다.  
코드는 위에서 아래로 이야기처럼 읽혀야 좋다. 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.  

<br>

### 서술적인 이름을 사용하라 (Use Descriptive Names)

함수 이름이 길어도 괜찮다. 길고 서술적인 이름이 짧고 어려운 이름보다 좋다.  
길고 서술적인 이름이 길고 서술적인 주석보다 좋다.  
이름을 붙일 때는 일관성이 있어야 한다. 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용한다.

<br>

### 함수 인수 (Function Arguments)

함수의 인수는 **최소한**으로 줄여야 한다.  
- **0개 (niladic)**: 가장 이상적이다.  
- **1개 (monadic)**: 가장 흔하다.  
- **2개 (dyadic)**: 조금 복잡해진다.
- **3개 (triadic)**: 특별한 이유가 없다면 피해야 한다.

<br>

### 부수 효과를 일으키지 마라 (Have No Side Effects)

함수는 **부수 효과 (Side Effect)**가 없어야 한다.  
부수 효과는 함수가 예상치 못한 값을 변경하거나 외부 상태에 영향을 미치는 경우를 의미한다.

<br>

### 명령과 조회를 분리하라 (Command Query Separation)

함수는 객체 상태를 변경하거나 아니면 객체 정보를 반환하거나 둘 중 하나만 해야 한다. 두 작업을 하나의 함수에서 동시에 수행하면 함수의 의도가 모호해진다.

```python
if handler.set("username", "unclebob"):
```
문장의 의도가 불분명하다.  
- "값이 이미 설정되어 있는지"를 확인하는 것인가?  
- 아니면 "username"을 "unclebob"으로 설정하는 것인가?

```python
if handler.attribute_exists("username"):
    handler.set_attribute("username", "unclebob")
```
위 코드는 두 가지 작업을 분리하여 명확하게 표현한다. 명령과 조회를 분리해 혼란을 없앤다.

<br>

### 반복하지 마라 (Don’t Repeat Yourself)

중복은 코드 길이가 늘어날 뿐 아니라 알고리즘이 변하면 모두 수정해야 하는 문제를 일으킨다. 어느 한 곳이라도 수정을 놓치면 오류가 발생할 수 있다.  
객체지향 프로그래밍은 코드를 부모 클래스로 몰아 중복을 없앤다.
