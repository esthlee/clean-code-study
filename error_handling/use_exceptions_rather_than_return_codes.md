# Chapter 7: Error Handling

## 핵심 원칙

### 1. **오류 코드보다 예외를 사용하라**
오류가 발생하는 경우 예외를 던지면 호출자 코드가 더 깔끔해진다. 논리와 오류 처리 코드가 뒤섞이지 않는다.

```java
public class FileController {
    public void deleteFile(String filePath) {
        try {
            tryToDeleteFile(filePath);
        } catch (FileDeleteError e) {
            logger.log(e.getMessage()); // 오류 로그 기록
        }
    }

    private void tryToDeleteFile(String filePath) throws FileDeleteError {
        File file = getFile(filePath);          // 파일 가져오기
        verifyFile(file);                       // 파일 유효성 검사
        deleteFileFromSystem(file);             // 파일 삭제
    }

    private File getFile(String filePath) throws FileDeleteError {
        if (filePath == null || filePath.isBlank()) {
            throw new FileDeleteError("유효하지 않은 파일 경로입니다: " + filePath);
        }
        return new File(filePath);
    }

    private void verifyFile(File file) throws FileDeleteError {
        if (!file.exists()) {
            throw new FileDeleteError("파일이 존재하지 않습니다: " + file.getPath());
        }
        if (!file.canWrite()) {
            throw new FileDeleteError("파일 삭제 권한이 없습니다: " + file.getPath());
        }
    }

    private void deleteFileFromSystem(File file) throws FileDeleteError {
        if (!file.delete()) {
            throw new FileDeleteError("파일 삭제 실패: " + file.getPath());
        }
        System.out.println("파일 삭제 성공: " + file.getPath());
    }
}
```

<br>

### 2. **Try-Catch-Finally 문부터 작성하라**
try 블록에서 무슨 일이 생기든 catch 블록은 프로그램 상태를 일관성 있게 유지해야 한다. 그러므로 예외가 발생할 코드를 짤 때는 Try-Catch-Finally 문으로 시작하는 게 좋다. try 블록에서 무슨 일이 생기든지 호출자가 기대하는 상태를 정의하기 쉽다.

<br>

### 3. **미확인(unchecked) 예외를 사용하라**
확인된(checked) 예외는 OCP(Open Closed Principle)를 위반한다.  
메서드에서 확인된 예외를 던졌는데 catch 블록이 몇 단계 위에 있다면 그 사이 메서드 모두 선언부에 해당 예외를 정의해야 한다. 하위 단계에서 코드를 변경하면 상위 단계 메서드 선언부를 전부 고쳐야 한다.  
throws 경로에 위치하는 모든 함수가 최하위 함수에서 던지는 예외를 알아야 하므로 캡슐화가 깨진다.  
때로는 확인된 예외도 유용하다. 아주 중요한 라이브러리를 작성한다면 모든 예외를 잡아야 한다. 그러나 일반적인 애플리케이션은 의존성이라는 비용이 이익보다 크다.

<br>

### 4. **예외에 의미를 제공하라**
예외를 던질 때는 전후 상황을 충분히 덧붙인다. 그러면 오류가 발생한 원인과 위치를 찾기 쉽다.  
오류 메시지에 정보를 담아 예외와 함께 던진다. 실패한 연산 이름과 실패 유형도 언급한다. 애플리케이션이 로깅 기능을 사용한다면 catch 블록에서 오류를 기록한다.

<br>

### 5. **호출자를 고려해 예외 클래스를 정의하라**
잘못된 예외 처리는 코드의 복잡성을 증가시키고 유지보수를 어렵게 만든다.
따라서 예외를 적절히 래핑(Wrapping) 하여 공통적인 예외 클래스를 정의하는 것이 중요하다.  
아래는 오류 분류가 좋지 않은 예시다.

```python
def read_file(file_path):
    try:
        with open(file_path, "r") as file:
            return file.read()
    except FileNotFoundError as e:
        log_error("파일을 찾을 수 없음", e)
        raise
    except PermissionError as e:
        log_error("파일 권한 오류", e)
        raise
    except IsADirectoryError as e:
        log_error("파일이 아니라 디렉토리임", e)
        raise
```

- 중복 코드 발생: log_error() 호출과 raise 문이 반복된다.
- 예외가 많아질수록 코드가 길어짐: 새로운 예외가 추가될 때마다 except 블록을 계속 추가해야 한다.
- 호출자가 여러 개의 예외를 알아야 함: 호출자는 FileNotFoundError, PermissionError, IsADirectoryError 등을 각각 처리해야 한다.

```python
class FileReadError(Exception):
    """파일 읽기 작업 중 발생하는 예외를 래핑하는 클래스"""
    def __init__(self, message, original_exception):
        super().__init__(message)
        self.original_exception = original_exception

def read_file(file_path):
    try:
        with open(file_path, "r") as file:
            return file.read()
    except (FileNotFoundError, PermissionError, IsADirectoryError) as e:
        raise FileReadError("파일을 읽는 중 오류 발생", e)
```

```python
try:
    content = read_file("data.txt")
except FileReadError as e:
    log_error(e)
```

예외를 하나의 공통 예외(FileReadError) 로 변환하여 처리하면 코드가 훨씬 단순해진다.
- 코드 중복 제거: 여러 개의 except 블록을 하나로 통합하여 FileReadError 예외로 변환한다.
- 코드 유지보수 용이: 새로운 파일 관련 예외가 생겨도 read_file 내부에서만 수정하면 된다.
- 호출부 코드 단순화: 호출자는 다양한 예외를 신경 쓰지 않고 FileReadError 하나만 처리하면 된다.

<br>

### 6. **정상 흐름을 정의하라**
아래는 직원의 식비를 조회하는 과정에서 식비 정보가 없는 경우 예외를 발생시키는 코드이다.

```python
class MealExpensesNotFound(Exception):
    """식비 정보를 찾을 수 없는 경우 발생하는 예외"""
    pass

class ExpenseReportDAO:
    def get_meals(self, employee_id):
        # 데이터베이스에서 식비 정보를 가져온다.
        # 식비 정보가 없는 경우 예외를 발생시킨다.
        raise MealExpensesNotFound()

def get_meal_per_diem():
    """식비가 없을 경우 기본 지급 금액 반환"""
    return 10000

employee_id = 1
total = 0

try:
    expenses = ExpenseReportDAO().get_meals(employee_id)
    total += expenses.get_total()
except MealExpensesNotFound:
    total += get_meal_per_diem()

print(f"총 식비: {total}")
```

- get_meals() 메서드에서 식비 정보가 없는 경우 예외를 발생시킨다.
- 예외 처리를 위해 try-except 블록을 사용해야 하므로 코드가 복잡해진다.
- 예외가 발생하는 흐름이 코드의 주 흐름을 방해한다.

```python
class MealExpenses:
    """식비 정보를 저장하는 클래스"""
    def get_total(self):
        raise NotImplementedError()

class ActualMealExpenses(MealExpenses):
    """실제 식비 정보를 저장하는 클래스"""
    def __init__(self, total):
        self._total = total

    def get_total(self):
        return self._total

class PerDiemMealExpenses(MealExpenses):
    """식비 정보가 없는 경우 기본값을 반환하는 클래스"""
    def get_total(self):
        return get_meal_per_diem()

class ExpenseReportDAO:
    def get_meals(self, employee_id):
        # 데이터베이스에서 식비 정보를 조회한다.
        # 만약 식비 정보가 없다면 PerDiemMealExpenses 객체를 반환한다.
        return PerDiemMealExpenses()

def get_meal_per_diem():
    """식비가 없을 경우 기본 지급 금액 반환"""
    return 10000

employee_id = 1
total = 0

expenses = ExpenseReportDAO().get_meals(employee_id)
total += expenses.get_total()

print(f"총 식비: {total}")
```

특수 사례 패턴(Special Case Pattern)을 적용하면 예외가 발생하는 대신, 식비 정보가 없는 경우 기본값을 반환하는 객체를 활용할 수 있다.

- try-except 블록이 제거되어 코드가 간결해진다.
- ExpenseReportDAO.get_meals() 메서드가 항상 MealExpenses 객체를 반환하므로 예외 처리 없이 get_total()을 호출할 수 있다.
- 예외를 발생시키지 않고, 특별한 경우(식비 정보가 없는 경우)를 다루는 객체(PerDiemMealExpenses)를 사용하여 처리한다.

<br>

## 요약

1. 깨끗한 코드는 읽기 좋으면서 안정성이 높아야 한다.
2. 오류 처리를 프로그램 논리와 분리해 독자적인 사안으로 고려한다.
3. 오류 처리를 프로그램 논리와 분리하면 코드 유지보수가 쉬워진다.
