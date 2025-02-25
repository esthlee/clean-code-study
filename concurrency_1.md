# 13장 동시성 

## 동시성이 필요한 이유?
- 즉, 한 프로그램에서 여러 개의 작업을 "겉보기에는 동시에" 실행하는 것.
- 여러 작업을 "겹쳐서" 실행하는 것.
- 응답시간과 작업 처리량 개선에도 효과적.
- 하지만, 동시성은 구현하기 어렵다.
  
## 난관
- **동시성은 다소 부하를 유발한다.** 성능면에서 부하가 걸리며, 코드도 더 짜야한다.
- **동시성은 복잡하다.** 간단한 문제라도 복잡하다.
- **일반적으로 버그는 재현하기 어렵다.** 그래서 진짜 결함으로 간주되지 않고 일회성 문제로 여겨 무식하기 쉽다.
- **동시성을 구현하려면, 근본적인 설계 전략을 재고해야한다.**
  
## 동시성 방어 원칙
- 동시성 코드가 일으키는 문제로부터 시스템을 방어하는 원칙
  
1. 단일 책임 원칙
- 동시성 관련 코드는 다른 코드와 분리 해야한다.
  
2. 따름 정리: 자료 범위를 제한하라
- 공유 객체를 사용하는 코드 내 임계영역을 synchroniczed 키워드로 보호라도록 권장한다.
- 즉, 멀티스레드 환경에서 여러 스레드가 공유 객체(Shared Object)에 동시에 접근하는 경우, synchronized 키워드를 사용하여 임계영역(Critical Section)을 보호하라는 뜻.
- 임계 영역: 여러 개의 스레드가 동시에 접근하면 문제가 발생할 수 있는 코드 영역
```python
import threading

counter = 0  # 공유 변수

def increment():
    global counter
    for _ in range(100000):
        counter += 1  # 여러 스레드가 동시에 접근하면 문제 발생

threads = [threading.Thread(target=increment) for _ in range(5)]

for t in threads:
    t.start()
for t in threads:
    t.join()

print(f"최종 counter 값: {counter}")  # 예상: 500000, 실제: 실행할 때마다 다름
```
- **자료를 캡슐화하라, 공유 자료를 최대한 줄여라**.
  
3. 따름 정리: 자료 사본을 사용하라
- 공유 자료를 줄이려면, 처음부터 공유하지 않는 방법이 가장 좋다.
- 코드가 문제를 일으킬 가능성도 낮아진다.

4. 따름 정리: 스레드는 가능한 독립적으로 구현하라
- 자신만의 세상에 존재하는 스레드를 구현한다. (다른 스레드와 자료를 공유x)
- 모든 정보는 비공유 출처에서 가져오며 로컬 변수에 저장한다.
- 독자적인 스레드로, 돌려도 괜찮도록 자료를 독립적인 단위로 분할하라.
```python
import threading

def worker(thread_id):
    local_counter = 0  # 🔹 각 스레드마다 독립적인 변수 사용
    for _ in range(100000):
        local_counter += 1
    print(f"Thread-{thread_id}: 최종 local_counter 값 = {local_counter}")

threads = [threading.Thread(target=worker, args=(i,)) for i in range(5)]

for t in threads:
    t.start()
for t in threads:
    t.join()
```


## 라이브러리를 이해하라
- 언어가 제공하는 클래스를 검토하라.

## 실행 모델을 이해하라
