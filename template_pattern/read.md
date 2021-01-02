# Chapter 03 Template Method-하위 클래스에서 구체적으로 처리하기

## 개념
- 템플릿메쏘드 패턴은 상위 클래스가 뼈대가 되는 로직을 구성하고,<br/>
  하위 클래스들이 이 로직의 요소들을 각각 구현하는 패턴이다.
  
- Template Method 패턴은 말그대로 템플릿의 기능을 가진 패턴이다.<br/>
  상위 클래스쪽에 템플릿에 해당하는 메소드가 정의되어 있고, 그 메소드의 정의 안에는 추상 메소드가 사용되어 있어서<br/>
  상위 클래스만 봐도 추상 메소드를 어떻게 호출하고 있는지 알 수 있지만<br/>
  추상 메소드의 구현은 하위 클래스에서 되어 있기 때문에 실제 구체적인 내용은 하위클래스를 보아야 한다.<br/>
  이처럼 상위 클래스에서 처리의 뼈대를 정하고, 하위 클래스에서 그 구체적인 내용을 결정하는 디자인 패턴을 Template Method 패턴 이라고 부른다.<br/>

## 구조
![구조 ](https://user-images.githubusercontent.com/5352548/103448422-d3829a80-4cdc-11eb-8f48-07ab6da2c47f.png)

#### AbstractClass
- ConcreteClass 가 상속받아야 할 추상 클래스
- templateMethod() 에 클라이언트가 사용할 로직을 담는다.
- 각 로직의 요소는 method1(), method2() 에 담기며, 이는 ConcreteClass 에서 구현한다.

#### ConcreteClass
- AbstractClass 를 상속받아, 필요한 로직 요소 method() 를 필요에 맞게 구현한다.
  
## 예제 
![구](https://user-images.githubusercontent.com/5352548/103448408-95857680-4cdc-11eb-99d5-8a278c499152.png)

#### AbstractDisplay.java 
```java
public abstract class AbstractDisplay {
     
    public abstract void open();
    public abstract void print();
    public abstract void close();
     
    public final void display() {
        open();
        for(int i=0; i<5; i++){
            print();
        }
        close();
    }
}
```

#### charDisplay.java 
```java
public class CharDisplay extends AbstractDisplay{
     
    private char ch;
     
    public charDisplay(char ch){
        this.ch = ch;
    }
 
 
    @Override
    public void open() {
        System.out.print("<<");      
    }
 
    @Override
    public void print() {
        System.out.print(ch);
    }
 
    @Override
    public void close() {
        System.out.println(">>");
    }
}
```

#### StringDisplay.java
```java
public class StringDisplay extends AbstractDisplay {
 
    private String string;
    private int width;
 
 
    public StringDisplay(String string){
        this.string = string;
        this.width = string.getBytes().length;
    }
 
    @Override
    public void open() {
        printLine();
    }
 
 
    @Override
    public void print() {
        System.out.println("|" + string + "|");
    }
 
    @Override
    public void close() {
        printLine();
    }
 
 
    private void printLine() {
        System.out.print("+");
        for(int i=0; i<width; i++){
            System.out.print("-");
        }
        System.out.println("+");
    }
}
```

#### Main.java
```java
public class Main {
 
    public static void main(String[] args) {
        AbstractDisplay d1 = new charDisplay('H');
        AbstractDisplay d2 = new StringDisplay("hello world");
        AbstractDisplay d3 = new StringDisplay("가나");
         
        d1.display();
        d2.display();
        d3.display();
    }
}
```

### 장점
- 로직과 로직요소를 분리하여, 전체 로직은 동일하되 로직 요소를 각각 다르게할 수 있다.
- 같은 로직을 가지고 있는 알고리즘들을 큰 뼈대 / 작은 요소로 나누어 관리하기가 쉬울 때.
- 명확한 로직이 있고, 로직에 들어가는 변수들을 잘 컴포넌트화 시킬 수 있는 경우.

### 단점
- 추상 클래스와 구현 클래스가 강하게 연결되어 있다.
  -  상속 때문에 하향적으로 강해지는 한편, 상위 클래스의 메쏘드를 사용하므로 상향적으로도 강해진다.
- 로직 요소(추상 메쏘드) 가 많아지면 클래스 자체가 복잡해질 수 있다.

### 정리
- 템플릿 메소드 패턴은 "알고리즘의 뼈대"를 맞추는 것에 있습니다. <br/>
즉, 전체적인 레이아웃을 통일 시키지만 상속받은 클래스로 하여금 어느정도 유연성을 주도록하는 디자인 패턴입니다.

### 번외(https://yaboong.github.io/java/2018/09/25/interface-vs-abstract-in-java8/)
#### 인터페이스와 추상클래스의 차이

- 디자인 패턴 중 템플릿 메소드 패턴에 대해 정리해서 글을 쓰려고 했었다. <br/>
템플릿 메소드 패턴에서는 템플릿 메소드와 hook 메소드를 분리하여 일정한 프로세스를 가진 기능을 hook 단위로 분리시킨다. <br/>
이때 추상클래스를 하나 만들고, 템플릿 메소드에서는 hook 메소드를 호출하고, 추상클래스를 상속받아 구현한 클래스에서 hook 메소드들을 구현하는 방식이다.<br/>
그런데 자바8 부터는 인터페이스도 default 키워드를 통해 구현체를 가질 수 있는데 왜 추상클래스를 사용해야 하는지에 대한 의문<br/>
인터페이스를 사용해도 구현은 똑같이 할 수 있지만 추상클래스를 사용하여 hook 메소드들에 대한 엄격한 접근제어를 사용할 때, 템플릿 메소드 패턴을 강제할 수 있다는 결론<br/>

