# 6장 객체와 자료 구조

## 1. 자료 추상화 (Data Abstraction)

### **자료 추상화란?**
자료 추상화는 객체 내부 데이터를 숨기고, 자료를 조작할 방법만 외부에 제공하는 것. 
구현 세부 사항을 숨기고, 사용자는 자료가 어떻게 동작하는지 몰라도 사용할 수 있도록 설계하는 원칙이다.

### **예제**
#### 잘못된 방식 (데이터 노출):
```java
public class Point {
    public double x;
    public double y;
}
```
- 문제: x와 y 값이 외부에 노출되어 데이터를 직접 수정할 수 있음.

#### 개선된 방식 (추상화 사용):
```java
public interface Point {
    double getX();
    double getY();
    void setCartesian(double x, double y);
    double getR();
    double getTheta();
    void setPolar(double r, double theta);
}
```
- 장점: 내부 구현 방식(x, y → r, theta 변경 등)이 바뀌더라도 외부에 영향을 주지 않음.

<br>

## 2. 자료/객체 비대칭 (Data/Object Anti-Symmetry)

### **자료와 객체의 차이**
1. 자료 구조는 자료를 외부에 노출하고 동작(함수)은 포함하지 않음.  
2. 객체는 추상화 뒤로 자료를 숨기고 동작(함수)만 외부에 제공함.  
   예: 데이터 캡슐화를 활용한 클래스.

### **왜 객체와 자료 구조를 다르게 설계하는 것이 중요한가?**
- 객체는 동작을 중심으로 설계하고, 자료를 숨겨야 유지보수가 쉬움.
- 자료 구조는 자료를 표현하는 데 집중해야 단순함을 유지할 수 있음.
- 두 설계 방식을 적절히 구분하면 코드의 명확성과 유연성이 증가함.

<br>

## 3. 디미터 법칙 (The Law of Demeter)

### **디미터 법칙이란?**
객체는 자신이 소유한 객체와만 상호작용해야 한다는 원칙.

```java
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```
- 문제: ctxt가 너무 많은 객체와 직접적으로 의존하고 있어 변경에 취약함.

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```
- 장점: 호출이 단순화되고, 객체 간 의존성을 줄임.

<br>

## 4. 자료 전달 객체 (DTO: Data Transfer Object)

### **DTO란?**
데이터를 단순히 전달하기 위한 클래스. 함수 없이, 공개 변수만 포함함.

### **예제**
```java
public class Address {
    public String street;
    public String city;
    public String state;
    public String zip;
}
```
- Address 객체는 데이터를 담아 전달하는 용도로만 사용.

<br>

## 5. 요약

- 객체는 동작을 공개하고 자료를 숨긴다.
- 자료 구조는 별다른 동작 없이 자료를 공개한다.
