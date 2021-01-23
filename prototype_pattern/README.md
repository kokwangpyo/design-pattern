# Chapter 06 Prototype - 복사해서 인스턴스 만들기 

- url : https://m.blog.naver.com/PostView.nhn?blogId=tradlinx0522&logNo=220658014429&proxyReferer=https:%2F%2Fwww.google.com%2F


## 개념
- 프로토타입 패턴은 Original 객체를 새로운 객체에 복사하여 우리의 필요에 따라 수정하는 해주는 매커니즘입니다.
- 프로토타입 패턴은 생성 패턴(Creational Pattern)
  - 생성 패턴은 인스턴스를 만드는 절차를 추상화하는 패턴
    - 생성 패턴은 이들 클래스의 인스턴스들이 어떻게 만들고 어떻게 결합하는지에 대한 부분을 완전히 가려줍니다.
      - 생성 패턴을 이용하면 무엇이 생성되고, 누가 이것을 생성하며, 이것이 어떻게 생성되는지, 언제 생성할 것인지 결정하는 데 유연성을 확보
      
     
## 예제

![구조 ](https://user-images.githubusercontent.com/5352548/105565071-580a8b00-5d68-11eb-9439-aa6b836d9f84.png)

#### Product.java
```java
public interface Product extends Cloneable {
        public abstract void use(String s);
        public abstract Product createClone();
}
```

#### Manager.java
```java
package framework;
public class Manager {
 
       private HashMap showcase = new HashMap();
 
        public void register (String name, Product proto) {
                   showcase.put(name, proto);
        }
 
       public Product create(String protoname){
                   Product p = (Product) showcase.get(protoname);
                   return p.createClone();  // 복사된 값 리턴
       }
}
```


#### MessageBox.java
```java
public class MessageBox implements Product {
 
        private char decochar;
 
        public MessageBox(char decochar) {
               this.decochar = decochar;
         }
 
       @Override
        public void use(String s) {
               int length = s.getBytes().length;
               System.out.println("길이: "+ length);
 
           for(int i = 0; i < length + 4; i++) {
                 System.out.print(decochar);
            }
 
                System.out.println(" ");
                System.out.println(decochar + " " + s + " " + decochar);
 
           for(int i = 0; i < length + 4; i++){
                System.out.print(decochar);
            }
                System.out.println(" ");
       }

       /*
       * JAVA에서 clone()는 자신의 클래스 or 하위 클래스 에서만 호출할 수 있기 때문에
       * 다른 클래스의 요청으로 복제를 하는 경우에는 createClone과 같은 다른 메소드를
       * 이용해서 clone을 기술할 필요가 있습니다.
       */
      @Override
       public Product createClone() {
                Product p = null;
       try{
                p = (Product) clone();    
       /*
        * Cloneable 인터페이스를 구현한 클래스의 인스턴스는 clone 메소드를 호출하면 복사됩니다.
        * 그리고 clone()의 반환값은 복사해서 만들어진 인스턴스가 됩니다.
        * 내부에서 하는 일은 원래의 인스턴스와 같은 메모리를 확보해서, 그 인스턴스의 필드 내용을 복사하는 것입니다.
        */
         } catch(CloneNotSupportedException e) {
                e.printStackTrace();
         }
              return p;
         }
} 
```


#### Main.java
```java
public class Main {
 
public static void main(String[] args) {
 
    // 준비 
    Manager manager = new Manager();
    UnderlinePen upen = new UnderlinePen('~');
    MessageBox mbox =  new MessageBox('*');
    MessageBox sbox =  new MessageBox('/');
 
    manager.register("strong message", upen);
    manager.register("warning box", mbox);
    manager.register("slash box", sbox);
 
    // 생성 (clone() 된 값이 반환된다.)
    Product p1 = manager.create("strong message");
    p1.use("Hello, World");
    Product p2 = manager.create("warning box");
    p2.use("Hello, World");
    Product p3 = manager.create("slash box");
    p3.use("Hello, World");
    
    } 
}
```

## 프로토 타입 패턴이 필요한 경우
- 종류가 너무 많아 클래스로 정리되지 않는 경우
  - 이것을 모두 각각의  클래스로 만들면 클래스의 수가 너무 많아지기 때문에 소스 프로그램을 관리하기 힘들다.
```
  - 취급하는 오브젝트의 종류가 너무 많아서 각각을 별도의  클래스로 만들어 다수의 소스파일을 작성해야 하는 경우
  ~ 을 사용해서 문자열에 밑줄을 긋는 것
  * 을 사용해서 문자열에 테두리를 긋는 것
  / 을 사용해서 문자열에 테두리를 긋는 것
  … 등등
```
  
- 클래스로부터 인스턴스 생성이 어려운 경우
  - 생성하고 싶은 인스턴스가 복잡한 작업을 거쳐 만들어지기 때문에 클래스로부터 만들기가 매우 어려운 경우
```
예: 마우스를 사용한 도형 에디터 같은 프로그램
```
  
- Framework 와 생성할 인스턴스를 분리하고 싶은 경우
  - 인스턴스를 생성할 때의 Framework를 특정 클래스에 의존하지 않도록 만들고 싶은 경우입니다. 
  이 경우는 클래스 이름을 지정해서 인스턴스를 만드는 것이 아니라 이미 '모형' 이 되는 인스턴스를 등록해 두고, 
  그 등록된 인스턴스를 복사해서 인스턴스를 생성
  (인스턴스로부터 다른 인스턴스를 만드는 것이 복사기를 사용해서 서류를 복사하는 일과 비슷)
  (원래의 서류를 어떻게 만들었는지 몰라도 복사기로 같은 종류의 서류를 몇 장이라도 만들 수 있다.)
```
-> 예제에서 인스턴스의 복사(clone) 를 실행하는 부분을 framework 패키지 안에 설정합니다.
Manager 클래스의 create()는 클래스 이름 대신에 'strong message'나 'slash box'라는 문자열을 
인스턴스 생성을 위한 이름으로 제공하고 있습니다. 
이것은 JAVA라는 언어가 구비하고 있는 인스턴스 생성기구인 new Something() 이라는 형식을 
보다 넓게 이용해서 클래스 이름의 속박으로부터 framework를 분리할 수 있습니다.
```


## 고려 할 사항
- 예제에서 사용된 clone() 메소드는 Object 클래스의 메소드로 써, 얕은 복사로 동작합니다.
그렇기 때문에 깊은 복제를 사용해야 할 경우 clone()메소드를 재정의 해야 합니다.
위와 같은 이슈로 복제된 객체의 순환 참조나 깊은복사 얉은 복사에 대한 고민이 필요
```
만약에 복사할 객체가 다른 객체를 가지고 있다면, 객체에 대한 참조가 복사되는 것이지 객체 자체가 따로 복사되는 것은 아닙니다. 
만약에 객체를 깊은 복사로 완전히 복사할 필요가 있다면, clone 메서드를 오버라이드 하여 재정의해야 할 것입니다.
```

