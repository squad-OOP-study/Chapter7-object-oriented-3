# 디자인 패턴 part3
## 파사드 패턴

### 파사트 패턴이란
![image](https://user-images.githubusercontent.com/58967292/159138531-f81ea35c-25c5-44e9-8768-adb6985e74af.png)

* 어떤 소프트웨어의 다른 커다란 코드 부분에 대하여 간략화된 인터페이스를 제공해주는 디자인 패턴을 의미합니다. 
* 어댑터 패턴과 거의 유사하게 작동하지만 차이가 있다
* 어댑터 패턴- 원래 코드를 다른 코드로 작동할 수 있는 래퍼  제공
* 파사드 패턴 - 원래 코드를 더 간편하게(단순하게) 처리할 수 있느 래퍼 제공 
* 파사드 패턴을 활용하면 서브 시스템의 구성 요소를 보호할 수 있는데, 서브 시스템의 구성요소를 직접 호출하지 않으므로 잘못된 사용을 방지할 수 있습니다.
* 퍼사드 클래스가 서브시스템 클래스들을 캡술화 하는 것은 아니다. 다만 기능을 편하게 사용할 수 있도록 인터페이스를 제공해 줄 뿐
### 파사드 패턴 예시
```Kotlin
interface Switch {
    fun on()
    fun off()
}
```
```Kotlin
class Cooler : Switch {
    override fun on() {
        println("쿨러 작동 시작..")
    }

    override fun off() {
        println("쿨러 작동 끝..")
    }
}
```

```Kotlin
class Magnetron : Switch {
    override fun on() {
        println("마이크로파 발생기 켜짐.. 작동중")
    }

    override fun off() {
        println("마이크로파 발생기 꺼짐")
    }
}
```

```Kotlin
class TimeChecker(private val EXPIRED_TIME: Int, microwave: MicrowaveFacade) : Switch {
    private val timer: Timer
    private val task: TimerTask
    var count = 0
    var microwave: MicrowaveFacade

    init {
        count = EXPIRED_TIME / 1000
        timer = Timer()
        this.microwave = microwave
        task = object : TimerTask() {
            override fun run() {
                if (count > 0) {
                    println("Timer.. " + count-- + " 초")
                } else {
                    println("조리가 완료되었습니다!")
                    timer.cancel()
                    microwave.off()
                }
            }
        }
    }

    override fun on() {
        timer.schedule(task, 0, TIME_INTERVAL)
    }

    override fun off() {
        timer.cancel()
    }

    companion object {
        var TIME_INTERVAL: Long = 1000
    }
}
``` 
```Kotlin
class Turntable : Switch {
    override fun on() {
        println("Turntable이 움직입니다")
    }

    override fun off() {
        println("Turntable이 멈췄습니다")
    }
}
```
```Kotlin
class MicrowaveFacade {
    var cooler: Cooler
    var magnetron: Magnetron
    var timeCheck: TimeChecker
    var turntable: Turntable
    var mode: Mode
    var switches: Array<Switch>
    var food: String? = null
    var isActive = false

    constructor(
        cooler: Cooler,
        magnetron: Magnetron,
        timeCheck: TimeChecker,
        turntable: Turntable,
        mode: Mode,
    ) : super() {
        this.cooler = cooler
        this.magnetron = magnetron
        this.timeCheck = timeCheck
        this.turntable = turntable
        this.mode = mode
        switches = arrayOf(cooler, magnetron, timeCheck, turntable)
    }

    constructor(mode: Mode) : super() {
        cooler = Cooler()
        magnetron = Magnetron()
        timeCheck = TimeChecker(mode.getValue(), this)
        turntable = Turntable()
        this.mode = mode
        switches = arrayOf(cooler, magnetron, turntable, timeCheck)
    }

    fun on() {
        for (i in switches.indices) {
            switches[i].on()
        }
        isActive = true
    }

    fun off() {
        for (i in switches.indices) {
            switches[i].off()
        }
        isActive = false
    }

    fun getMode() {
        System.out.println("현재 모드는... " + mode.getName())
    }

    fun setMode(mode: Mode) {
        this.mode = mode
    }
}
```
```
fun main(args: Array<String>) {
    val microwave = MicrowaveFacade(Mode.FAST)
    microwave.on()
    }
}
```
### 파사드 패턴 장단점
* 장점
  * 클라이언트와 서브 시스템의 연결을 느슨하게 만듦
  * 단순한 인터페이스로 복잡한 서브시스템을 감쌈
* 단점
  * 다른 구성요소에 대한 메소드 호출을 처리하기 위해 레퍼 클래스를 많이 만들어야 할 수 있음
  * 시스템이 더 복잡해지고, 개발시간도 늘어나면서 성능의 저하도 있을 수 있음

## 추상 팩토리 패턴
### 추상 팩토리 패턴이란
* 팩토리 메서드 패턴: 조건에 따른 객체 생성을 팩토리 클래스로 위임하여, 팩토르 클래스에서 객체를 생성하는 패턴 
* 추상 팩토리 패턴: 서로 관련이 있는 객체들을 통째로 묶어서 팩토리 클래스로 만들고, 이들 팩토리를 조건에 따라 생성하도록 다시 팩토리를 만들어서 객체를 생성하는 패턴
* 추상 팩토리 패턴이 팩토리 메서드 패턴의 상위호환이 아니다.
* 두 패턴의 차이는 명확하기 때문에 상황에 따라 적절한 선택을 해야한다

### 예시코드
![image](https://user-images.githubusercontent.com/58967292/159139125-31d678e9-424c-407c-a9c8-ff65d7bcb15b.png)

```
public class LGKeyboard implements Keyboard {
    public LGKeyboard(){
        System.out.println("LG 키보드 생성");
    }
}
public class SamsungKeyboard implements Keyboard {
    public SamsungKeyboard(){
        System.out.println("Samsung 키보드 생성");
    }
}
public interface Keyboard {
}

public class KeyboardFactory {
    public Keyboard createKeyboard(String type){
        Keyboard keyboard = null;
        switch (type){
            case "LG":
                keyboard = new LGKeyboard();
                break;

            case "Samsung":
                keyboard = new SamsungKeyboard();
                break;
        }

        return keyboard;
    }
}
```
```
public class LGMouse implements Mouse {
    public LGMouse(){
        System.out.println("LG 마우스 생성");
    }
}
public class SamsungMouse implements Mouse {
    public SamsungMouse(){
        System.out.println("Samsung 마우스 생성");
    }
}
public interface Mouse {
}

public class MouseFactory {
    public Mouse createMouse(String type){
        Mouse mouse = null;
        switch (type){
            case "LG":
                mouse = new LGMouse();
                break;

            case "Samsung":
                mouse = new SamsungMouse();
                break;
        }

        return mouse;
    }
}
```
```
public class ComputerFactory {
    public void createComputer(String type){
        KeyboardFactory keyboardFactory = new KeyboardFactory();
        MouseFactory mouseFactory = new MouseFactory();

        keyboardFactory.createKeyboard(type);
        mouseFactory.createMouse(type);
        System.out.println("--- " + type + " 컴퓨터 완성 ---");
    }
}
```
```
public class Client {
    public static void main(String args[]){
        ComputerFactory computerFactory = new ComputerFactory();
        computerFactory.createComputer("LG");
    }
}
```
```
public class ComputerFactory {
    public void createComputer(String type){
        KeyboardFactory keyboardFactory = new KeyboardFactory();
        MouseFactory mouseFactory = new MouseFactory();
        BodyFactory bodyFactory = new BodyFactory();
        MonitorFactory monitorFactory = new MonitorFactory();
        SpeakerFactory speakerFactory = new SpeakerFactory();
        PrinterFactory printerFactory = new PrinterFactory();

        keyboardFactory.createKeyboard(type);
        mouseFactory.createMouse(type);
        bodyFactory.createBody(type);
        monitorFactory.createMonitor(type);
        speakerFactory.createSpeaker(type);
        printerFactory.createPrinter(type);
        System.out.println("--- " + type + " 컴퓨터 완성 ---");
    }
}
```

![image](https://user-images.githubusercontent.com/58967292/159139152-c1b42269-57a8-4a63-baac-f2eecfd06cd0.png)
```
public class SamsungComputerFactory implements ComputerFactory {
    public SamsungKeyboard createKeyboard() {
        return new SamsungKeyboard();
    }

    public SamsungMouse createMouse() {
        return new SamsungMouse();
    }
}
```
```
public class LGComputerFactory implements ComputerFactory {
    public LGKeyboard createKeyboard() {
        return new LGKeyboard();
    }

    public LGMouse createMouse() {
        return new LGMouse();
    }
}
```
```
public interface ComputerFactory {
    public Keyboard createKeyboard();
    public Mouse createMouse();
}
```
```
public class FactoryOfComputerFactory {
    public void createComputer(String type){
        ComputerFactory computerFactory= null;
        switch (type){
            case "LG":
                computerFactory = new LGComputerFactory();
                break;

            case "Samsung":
                computerFactory = new SamsungComputerFactory();
                break;
        }

        computerFactory.createKeyboard();
        computerFactory.createMouse();
    }
}
```
### 장단점
* 장점
  * 구체적인 클래스를 사용자로부터 분리 : 사용자는 생산되는 인터페이스에 대해서만 숙지하고 있어도 됨.
  * 비슷한 속성의 객체(같은 추상 제품을 상속받은 객체)들을 쉽게 대체하거나 교체할 수 있음.
  * 객체 생성 과정에서의 일관성이 높아짐

* 단점
  * 수정 불편 : 새로운 제품 클래스를 추가하게 된다면, 기존 추상 팩토리를 확장하기가 쉽지 않다. 추상 팩토리를 상속하고 있는 모든 팩토리에도 새로운 제품에 대한 구현 방법이 수정되어야 하기 때문이다.

## 컴포지트 패턴
### 컴퍼지트 패턴이란
![image](https://user-images.githubusercontent.com/58967292/159139479-dd40d7a2-70ef-4c17-8c27-65f92ffd905e.png)

* 여러 개의 객체들로 구성된 복합 객체와 단일 객체를 클라이언트에서 구별 없이 다루게 해주는 패턴
* 즉, 전체-부분의 관계(Ex. Directory-File)를 갖는 객체들 사이의 관계를 정의할 때 유용하다.
* 또한 클라이언트는 전체와 부분을 구분하지 않고 동일한 인터페이스 를 사용할 수 있다.

### 예시 코드
![image](https://user-images.githubusercontent.com/58967292/159139527-6b920d9f-5a9a-40fe-8df2-cee73a9d6c4b.png)
![image](https://user-images.githubusercontent.com/58967292/159139512-0c4c2e00-49da-4dc1-928c-330e60dc7565.png)
```
public class Keyboard {
  private int price;
  private int power;
  public Keyboard(int power, int price) {
    this.power = power;
    this.price = price;
  }
  public int getPrice() { return price; }
  public int getPower() { return power; }
}
```
```
public class Body { 동일한 구조 }
public class Monitor { 동일한 구조 }
```
```
public class Computer {
  private Keyboard Keyboard;
  private Body body;
  private Monitor monitor;

  public addKeyboard(Keyboard keyboard) { this.keyboard = keyboard; }
  public addBody(Body body) { this.body = body; }
  public addMonitor(Monitor monitor) { this.monitor = monitor; }

  public int getPrice() {
    int keyboardPrice = keyboard.getPrice();
    int bodyPrice = body.getPrice();
    int monitorPrice = monitor.getPrice();
    return keyboardPrice + bodyPrice + monitorPrice;
  }
  public int getPower() {
    int keyboardPower = keyboard.getPower();
    int bodyPower = body.getPower();
    int monitorPower = monitor.getPower();
    return keyboardPower + bodyPower + monitorPower;
  }
}
```
```
public class Client {
  public static void main(String[] args) {
    // 컴퓨터의 부품으로 Keyboard, Body, Monitor 객체를 생성
    Keyboard keyboard = new Keyboard(5, 2);
    Body body = new Body(100, 70);
    Monitor monitor = new Monitor(20, 30);

    // Computer 객체를 생성하고 부품 객체들을 설정
    Computer computer = new Computer();
    computer.addKeyboard(keyboard);
    computer.addBody(body);
    computer.addMonitor(monitor);

    // 컴퓨터의 가격과 전력 소비량을 구함
    int computerPrice = computer.getPrice();
    int computerPower = computer.getPower();
    System.out.println("Computer Price: " + computerPrice + "만원");
    System.out.println("Computer Power: " + computerPower + "W");
  }
}
```
![image](https://user-images.githubusercontent.com/58967292/159139951-3471eb1e-9314-435a-954d-d9ad8fa239a6.png)


```
public class Computer extends ComputerDevice {
  // 복수 개의 ComputerDevice 객체를 가리킴
  private List<ComputerDevice> components = new ArrayList<ComputerDevice>();

  // ComputerDevice 객체를 Computer 클래스에 추가
  public addComponent(ComputerDevice component) { components.add(component); }
  // ComputerDevice 객체를 Computer 클래스에서 제거
  public removeComponent(ComputerDevice component) { components.remove(component); }

  // 전체 가격을 포함하는 각 부품의 가격을 합산
  public int getPrice() {
    int price = 0;
    for(ComputerDevice component : components) {
      price += component.getPrice();
    }
    return price;
  }
  // 전체 소비 전력량을 포함하는 각 부품의 소비 전력량을 합산
  public int getPower() {
    int power = 0;
    for(ComputerDevice component : components) {
      price += component.getPower();
    }
    return power;
  }
}
```
```
public class Client {
  public static void main(String[] args) {
    // 컴퓨터의 부품으로 Keyboard, Body, Monitor 객체를 생성
    Keyboard keyboard = new Keyboard(5, 2);
    Body body = new Body(100, 70);
    Monitor monitor = new Monitor(20, 30);

    // Computer 객체를 생성하고 addComponent()를 통해 부품 객체들을 설정
    Computer computer = new Computer();
    computer.addComponent(keyboard);
    computer.addComponent(body);
    computer.addComponent(monitor);

    // 컴퓨터의 가격과 전력 소비량을 구함
    int computerPrice = computer.getPrice();
    int computerPower = computer.getPower();
    System.out.println("Computer Price: " + computerPrice + "만원");
    System.out.println("Computer Power: " + computerPower + "W");
  }
}
```
### 장단점
* 장점
  * 객체들이 모두 같은 타입으로 취급되기 때문에 새로운 클래스 추가가 용이합니다.
  * 단일 객체 및 집합 객체를 구분하지 않고 코드 작성이 가능하여 사용자 코드가 단순해집니다.
  * 단일 객체와 집합 객체로 구성된 하나의 일관된 클래스 계통을 정의합니다. 런타임 단일 객체와 집합 객체를 구분하지 않고 일관된 프로그래밍이 가능합니다.
* 단점
  * 설계가 지나치게 범용성을 많이 가집니다. 
  * 복합체의 구성 요소에 제약을 가하기 힘듭니다. 
  * 복합체가 오직 한개의 구성 요소만 있었으면 할때가 있는데 Composite 클래스만 가지고는 이를 제어하기 어렵다


## 널 객체 패턴
### 널 객체 패턴이란
* 유저가 직접 null check를 하지 않고, 정의된 액션을 수행을 하지만 수행의 내용은 비어있는 빈 객체를 기본으로 제공해 줌으로서 예외 처리를 사전에 피하는 안전한 방식의 프로그래밍 패턴이다.

### 장단점
* 장점
  * 클라이언트 코드에서 null 값을 비교하는 구문을 최소화 할수 있다.
  * 라이브러리를 제공하는 측면에서 안정성있는 API를 제공할수 있다.

* 단점
  * Null 객체를 요구하는 클래스마다 인터페이스를 선언하고 이를 구현하는 Null 객체를 만들어주어야 한다. 
  * 클라이언트에서 Null 객체의 존재를 모르는 경우, 디버깅시에 약간의 혼선이 올 수 있다.
  * 널 검사가 별로 필요하지 않는 로직의 경우 구조만 복잡하게 만들수 있다-> 유지 보수가 복잡해진다 
