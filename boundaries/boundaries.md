# 8장 경계 
- 외부코드를 우리 코드에 깔끔하게 처리하고 통합하는 기법의 필요성.

## 외부 코드 사용하기 
### 새로운 클래스로 경계를 감싼다. 
- 외부 라이브러리나 패키지의 코드를 직접 사용하는 대신, 새로운 클래스를 작성해 외부 패키지와 내부 코드 간의 "경계"를 만든다.
```python
# 외부 패키지 (예: requests 라이브러리)
import requests

# 경계를 감싸는 클래스
class HttpClient:
    def get(self, url):
        response = requests.get(url)
        return response.json()

# 내부 코드에서는 HttpClient를 사용
def fetch_data():
    client = HttpClient()
    data = client.get("https://jsonplaceholder.typicode.com/posts/1")
    print(data)

fetch_data()
```
- 나중에 requests를 다른 라이브러리(예: httpx)로 교체해도 HttpClient만 수정하면 된다.

### 어댑터 패턴
- 외부 라이브러리가 제공하는 인터페이스를 우리가 원하는 방식으로 변환하는 패턴.
```python
# 외부 패키지
import requests

# 외부 API가 제공하는 인터페이스
class ExternalAPI:
    def fetch(self, endpoint):
        response = requests.get(endpoint)
        return response.text  # 원래는 JSON으로 반환되지 않음

# 우리가 원하는 인터페이스
class Adapter:
    def __init__(self, external_api):
        self.external_api = external_api

    def get_data(self, endpoint):
        # 외부 API 호출 결과를 JSON으로 변환
        raw_data = self.external_api.fetch(endpoint)
        # 변환 로직 (JSON 파싱 등)
        return {"data": raw_data[:50]}  # 예: 첫 50자를 가공해서 반환

# 내부 코드
def process_data():
    api = ExternalAPI()
    adapter = Adapter(api)  # 어댑터를 통해 외부 API 사용
    data = adapter.get_data("https://jsonplaceholder.typicode.com/posts/1")
    print(data)

process_data()
```
- 외부 API가 변경되더라도 어댑터만 수정하면 된다.
  
## 경계살피고 익히기 
- 외부 코드는 보통 익히기 어렵고, 통합하기도 어렵다.
- 우리쪽 코드를 작성해 외부 코드를 호출하는 대신 먼저 간단한 테스트 케이스를 작성해 외부 코드를 익힌다. -> 학습 테스트
- 학습테스트는 프로그램에서 사용하는 방식대로 외부 API를 호출한다.
- logging 학습 테스트 예제
```python
import logging

# 학습 테스트: 로깅 설정 및 메시지 출력
def test_logging_basic():
    # 1. 로거(logger) 생성
    logger = logging.getLogger("TestLogger")
    
    # 2. 로깅 레벨 설정 (DEBUG, INFO, WARNING, ERROR, CRITICAL)
    logger.setLevel(logging.DEBUG)
    
    # 3. 핸들러(handler) 추가: 콘솔 출력
    console_handler = logging.StreamHandler()
    console_handler.setLevel(logging.DEBUG)
    
    # 4. 포매터(formatter) 설정
    formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    console_handler.setFormatter(formatter)
    
    # 5. 핸들러를 로거에 추가
    logger.addHandler(console_handler)
    
    # 테스트: 로그 메시지 출력
    logger.debug("This is a DEBUG message")
    logger.info("This is an INFO message")
    logger.warning("This is a WARNING message")
    logger.error("This is an ERROR message")
    logger.critical("This is a CRITICAL message")
    
    # 검증
    assert logger.level == logging.DEBUG, "로거의 기본 레벨은 DEBUG로 설정되어야 합니다."
    print("Logging test completed successfully!")

# 학습 테스트 실행
if __name__ == "__main__":
    test_logging_basic()```

## 아는 코드와 모르는 코드를 분리하는 경계
- 우리가 필요한 인터페이스를 정의하면 코드는 훨씬 깔끔해지고, 가독성은 높아진다.

## 깨끗한 경계
- 경계에 위치하는 코드는 깔끔히 분리한다.
- 통제가 불가능한 외부 패키지에 의존하는 대신 통제가 가능한 우리 코드에 의존하는 편이 훨씬 좋다.
- 외부 패키지를 호출하는 코드를 가능한 줄여 경계를 관리하자.
- 새로운 클래스로 경계를 감싸거나, adapter 패턴을 사용해 우리가 원하는 인터페이스를 패키지가 제공하는 인터페이스로 변환하자. 
