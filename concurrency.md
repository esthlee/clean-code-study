# 13장 동시성 

## 동시성이 필요한 이유?
- 즉, 한 프로그램에서 여러 개의 작업을 "겉보기에는 동시에" 실행하는 것.
- 여러 작업을 "겹쳐서" 실행하는 것.
- 응답시간과 작업 처리량 개선에도 효과적.
- 하지만, 동시성은 구현하기 어렵다.

<br>

## 난관
- **동시성은 다소 부하를 유발한다.** 성능면에서 부하가 걸리며, 코드도 더 짜야한다.
- **동시성은 복잡하다.** 간단한 문제라도 복잡하다.
- **일반적으로 버그는 재현하기 어렵다.** 그래서 진짜 결함으로 간주되지 않고 일회성 문제로 여겨 무시하기 쉽다.
- **동시성을 구현하려면, 근본적인 설계 전략을 재고해야한다.**

<br>

## 동시성 방어 원칙
- 동시성 코드가 일으키는 문제로부터 시스템을 방어하는 원칙
  
1. 단일 책임 원칙
- 동시성 관련 코드는 다른 코드와 분리 해야한다.
  
2. 따름 정리: 자료 범위를 제한하라
- 프로세스: 프로그램 실행 인스턴스 (크롬 브라우저)
- 스레드 : 그 안에서 동시에 실행할 있는 작은 작업 단위, 스레드 여러개: 동시 처리 가능 (탭 여러개) 
- 공유 객체: 여러 스레드가 같이 사용하는 데이터 (공용 리스트, 숫자)
- 임계 영역: 동시에 접근하면 안되는 중요한 코드 부분
- synchronized 키워드 : 한 번에 한 스레드만 임계영역에 들어가게 막는 키워드
- 공유 객체를 사용하는 코드 내 임계영역을 synchroniczed 키워드로 보호라도록 권장한다. (헌 번에 한 스레드만 그 코드에 들어가게 막는 것)

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

<br>

## 라이브러리를 이해하라
- 언어가 제공하는 클래스를 검토하라.

<br>

## 실행 모델을 이해하라
### 다중 스레드 프로그램에서 자주 발생하는 개념 
- 한정된 자원: 제한된 수의 자원을 뜻함. 예시. 서버에서 동시에 사용할 수 있는 DB연결, 버퍼 등 무한하지 않고 제한적이기 때문에 여러 스레드가 이 자원을 서로 나눠 써야해서 경쟁이 발생.
  비유) 식당에 자리가 10개 뿐인데, 손님은 50명. 누가 먼저 앉느냐가 문제.
  
- 상호 배체: 여러 스레드가 같은 자원을 동시에 건드리면, 문제가 생기기 때문에 한번에 하나만 접근하게 막는 개념. 예. Lock
  비유) 화장실 하나를 여러명이 쓴다면? => 한 명씩 들어가야 안전하다.

- 기아: 자원을 계속 기다리기만 하고 얻지 못하는 상태. 항상 특정 스레드만 우선순위가 높아서 먼저 자원을 가져가면, 다른 스레드는 기다리기만 하다 굶게 된다.
  비유) 맛집 줄 서 있는데, 매번 VIP 손님이 새치기 한다. 일반 손님은 못 먹는다 -> 기아 상태

- 데드락: 스레드들이 서로가 가진 자원을 기다리면서 아무도 일을 못하게 되는 상태.
  비유) 두 사람이 서로 열쇠를 들고 교환하려는데, 서로 손을 안 놓으면 아무것도 못함.

- 라이블락: 데드락처럼 멈춰 있지 않고, 계속 바쁘게 움직이지만 일은 안되는 상황.
  비유) 복도에서 두 사람이 마주쳐서 계속 길 피해주는데… 계속 피해만 다녀서 결국 못 지나감

<br>

## 동기화하는 메서드 사이에 존재하는 의존성을 이해하라

동기화된 메서드 간의 의존성은 동시성 코드에서 미묘한 버그를 유발할 수 있다. Java에서는 `synchronized` 키워드를 사용하여 개별 메서드를 보호할 수 있지만, 동일한 공유 객체에서 여러 개의 동기화된 메서드를 사용하면 시스템이 올바르게 동작하지 않을 가능성이 크다.

### 권장 사항
- 공유 객체에서 두 개 이상의 동기화된 메서드를 사용하는 것을 피한다.
- 반드시 여러 개의 메서드를 사용해야 하는 경우, 다음 세 가지 방법을 고려한다.
  1. **클라이언트 기반 락(Client-Based Locking)**  
     - 클라이언트가 서버를 잠근 후 첫 번째 메서드를 호출하고, 마지막 메서드를 호출할 때까지 락을 유지한다.
  2. **서버 기반 락(Server-Based Locking)**  
     - 서버 내부에서 하나의 메서드가 모든 메서드를 호출한 후 락을 해제하도록 설계한다.
  3. **연결 서버(Adapted Server)**  
     - 기존 서버를 변경할 수 없을 경우, 중간 계층을 추가하여 동기화를 수행한다.

<br>

## 동기화하는 부분을 작게 만들어라

`synchronized` 키워드는 락을 도입하며, 동일한 락이 걸린 코드 블록에서는 한 번에 하나의 스레드만 실행될 수 있다. 락은 성능 저하를 유발할 수 있으므로 불필요한 `synchronized` 문 사용을 피해야 한다.

### 권장 사항
- 반드시 보호해야 하는 임계 영역(critical section)만 동기화한다.
- 동기화 블록을 가능한 한 작게 유지하여 성능 저하를 방지한다.
- 큰 동기화 블록을 만들면 스레드 간에 경쟁(contention)이 늘어나고 성능이 저하될 수 있으므로 피해야 한다.

<br>

## 올바른 종료 코드는 구현하기 어렵다

애플리케이션이 지속적으로 실행되는 시스템을 만드는 것과 올바르게 종료되는 시스템을 만드는 것은 다르다. 올바른 종료를 구현하는 것은 예상보다 어렵다.

### 주요 문제
- **데드락(Deadlock)**  
  - 종료 시 신호를 기다리는 스레드가 영원히 대기 상태가 될 수 있다.
- **부적절한 종료 시퀀스**  
  - 예를 들어, 부모 스레드가 모든 자식 스레드가 종료될 때까지 기다린다고 가정할 때, 특정 자식 스레드가 데드락 상태에 빠지면 부모 스레드는 무한 대기 상태가 된다.
  - 생산자-소비자 모델에서 생산자가 먼저 종료되면 소비자는 데이터가 도착하기를 기다리다가 종료되지 않을 수 있다.

### 권장 사항
- 종료 방식을 초기에 설계하고 충분히 테스트한다.
- 기존의 종료 알고리즘을 참고하여 신뢰할 수 있는 방법을 채택한다.

<br>

## 스레드 코드 테스트하기

멀티스레드 코드에서의 오류를 증명하는 것은 현실적으로 불가능하다. 하지만 철저한 테스트를 통해 리스크를 최소화할 수 있다.

### 권장 사항
- 문제를 노출하는 테스트 케이스를 작성한다.
- 다양한 환경과 부하에서 테스트를 수행하고, 오류가 발생하면 반드시 원인을 분석한다.

### 지침 사항
- 말이 안 되는 실패는 잠정적인 스레드 문제로 취급한다.
- 다중 스레드를 고려하지 않은 순차 코드부터 제대로 돌게 만든다.
- 다중 스레드를 쓰는 코드 부분을 다양한 환경에 쉽게 끼워 넣을 수 있도록 스레드 코드를 구현한다.
- 다중 스레드를 쓰는 코드 부분을 상황에 맞춰 조정할 수 있게 작성한다.
- 프로세서 수보다 많은 스레드를 돌려본다.
- 다른 플랫폼에서 돌려본다.
- 코드에 보조 코드를 넣어 돌린다. 강제로 실패를 일으키게 해본다.
