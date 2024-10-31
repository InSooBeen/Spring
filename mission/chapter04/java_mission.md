## 추가과제

---

### 함수형 인터페이스

---

- 추상메서드가 하나만 정의된 인터페이스 ( default, static 메서드는 여러 개가 존재해도 상관 없음)
- 람다 표현식을 이용해 함수형 프로그래밍을 구현하기 위한 목적
- 함수의 형태가 다양하므로, 일반적으로는 이미 만들어진 함수형 인터페이스 표준 API(java.util.function 패키지)를 사용

### 표준 API 종류

---

**Runnable**

- 메서드: void run()
- 매개변수와 리턴값이 없는 형태
- 주로 스레드의 매개변수로 사용
- 람다식: ()→void

**Consumer<T>**

- 메서드: void accept(T t)
- 매개변수는 사용하고 리턴값이 없는 형태
- 람다식: T→void

**Supplier<T>**

- 메서드: T get()
- 매개변수가 없고 리턴값이 있는 형태
- 람다식: ()→T

**Function<T, R>**

- 메서드: R apply(T t)
- 매개변수 값을 타입변환해서 리턴하는 형태
- 람다식: T→R

**Predicate<T>**

- 메서드: boolean test(T t)
- 매개변수 값이 조건에 맞는지 단정해서 boolean 리턴하는 형태
- 람다식: T→boolean

**UnaryOperator**

- 메서드: T apply(T t)
- 매개변수 값을 동일한 타입의 결과로 리턴하는 형태
- 람다식: T→T

**BinaryOperator**

- 메서드: T apply(T t1, T t2)
- 두 개의 매개변수 값을 동일한 타입의 결과로 리턴하는 형태
- 람다식: T→T

## 문제풀이

```java
import java.util.function.*;
import java.util.Scanner;

//2.Function을 구현한 클래스 정의
class MyFunction implements Function<Integer, String>{
    public MyFunction(){}
    @Override
    public String apply(Integer integer) {
        return integer.toString();
    }
}

public class FunctionInterface {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        int n;
        //1.익명 클래스 정의
        Function<Integer, String> fun1 = new Function<Integer, String>(){
            @Override
            public String apply(Integer integer){
                return integer.toString();
            }
        };
        //출력 1
        System.out.print("정수를 입력하세요: ");
        n = scan.nextInt();
        System.out.println(fun1.apply(n));

        //출력 2
        System.out.print("정수를 입력하세요: ");
        n = scan.nextInt();
        MyFunction my = new MyFunction();
        System.out.println(my.apply(n));

        //3.람다식으로 정의
        Function<Integer, String> fun3 = (Integer integer) -> integer.toString();
        //출력 3
        System.out.print("정수를 입력하세요: ");
        n = scan.nextInt();
        System.out.println(fun3.apply(n));

        //4.메서드 참조로 정의
        Function<Integer, String> fun4 = String::valueOf;
        //출력 4
        System.out.print("정수를 입력하세요: ");
        n = scan.nextInt();
        System.out.println(fun4.apply(n));

    }
}

```

### Stream API

---

- 데이터의 흐름을 표준화된 방법으로 쉽게 처리할 수 있도록 지원하는 패키지
- Collection 인터페이스 내에 존재하는 메서드
- 컬렉션 데이터의 효율적인 처리를 가능하게 해주는 기능
- 코드의 가독성과 성능을 높일 수 있음

### Stream이란?

---

- 데이터의 흐름을 의미함
- 데이터를 함수형 연산을 통해 표준화된 방법으로 쉽게 가공, 처리할 수 있음

**장점**

- 동작을 하나하나 기술하는 명령형 코딩 방식에서 벗어나 무엇을 어떻게 할지를 기술하는 선언형 코딩이 가능해짐 ⇒ 가독성 향상
- 데이터의 흐름을 멀티 스레드로 나눠 병렬 처리한 뒤 합치는 과정을 통해 대량의 데이터를 빠르게 처리할 수 있음
- 복잡한 반복문과 조건문을 간결하고 명확한 코드로 바꿔 유지보수가 용이해짐




### Stream의 처리 구조

---

**1단계: Stream 생성**

- 데이터 컬렉션을 stream으로 변환하는 과정
- Stream API를 사용하기 위해 최초 1번은 실행되어야 함
- 모든 데이터가 메모리에 로드되지 않고, 필요할 때만 로드 됨

**2단계: 가공**

- 소스의 데이터 집합을 원하는 형태로 가공하는 중간 처리 단계
- filter, map, sort 등이 해당됨
- 중간 연산의 입력값, 출력값은 모두 stream ⇒ 결과물이 stream이므로 연속적으로 연산을 수행할 수 있음

  **3단계 : 소비**

- Stream에 대한 최종 연산을 수행하여 최종 결과물을 얻는 것
- 최종 연산은 최종 결과물을 얻기 위한 목적이며 1회만 수행 가능(최종 연산 후 stream이 닫히기 때문)

**지연평가**

- Stream 데이터 연산 처리의 특징
- 중간 연산들은 연산을 호출할 때 즉시 수행되지 않고, 최종 연산이 호출될 때까지 지연
- Stream 데이터 처리는 모든 데이터에 대해 하나의 함수가 끝나고(모두 처리되고 나서) 다른 함수가 수행되는 것이 아니라, 앞선 데이터가 먼저 처리된 후 뒤의 데이터가 나중에 처리되는 방식

## 문제 풀이

```java
import java.util.*;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class StreamApi {
    public static void main(String[] args) {

        //1.초기 배열 만들기
        int[] array = new int[]{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        List<Integer> list = Arrays.stream(array)
                .boxed()
                .collect(Collectors.toList());

        //초기 배열 출력
        for(int i : list){
            System.out.print(i+" ");
        }
        System.out.println();

        //2.각 요소를 2배로 한 배열 만들기
        List<Integer> doubleArr = list.stream()
                .map(num -> num*2)
                .collect(Collectors.toList());

        //2배로 한 배열 출력
        for(int i : doubleArr){
            System.out.print(i+" ");
        }
        System.out.println();

        //3.짝수만 String으로 바꾼 배열 만들기
        List<String> evenArr = list.stream()
                .filter( num -> num % 2 == 0)
                .map(String::valueOf)
                .map( num -> num +" is even number")
                .collect(Collectors.toList());

        //짝수만 String으로 바꾼 배열 출력
        for(String s : evenArr){
            System.out.println(s+" ");
        }
        System.out.println();
    }
}

```

## 면접 질문

---

**함수형 인터페이스란?**

- 추상 메서드가 하나인 인터페이스이며, default와 static 메서드의 유무에 영향받지 않는다.
- 자바 8 이전에는 클래스 내에 직접 익명 클래스를 만들어 사용했으나, 8 이후부터는 람다식 표현식을 통해 간략하게 구현할 수 있게 되었다.
- 일반적으로는 이미 만들어진 함수형 인터페이스 표준 API(java.util.function 패키지)를 사용한다.

**스트림과 컬렉션의 차이는?**

- 연산 처리 : 컬렉션은 메서드를 호출하면  데이터를 즉시 처리하여 결과를 반환하지만, 스트림은 중간연산을 지연시켰다가 최종 연산이 호출되면 중간 연산이 실행된다.
- 병렬 처리: 컬렉션은 일반적으로 단일 스레드에서 작업하며 병렬처리를 위해서는 직접 코드를 작성해야하지만, 스트림은  parallelStream() 메서드를 통해 병렬처리를 지원하고, 대량의 데이터를 쉽게 처리할 수 있다.