
## 파사드(Facade) 패턴

- 파사드 패턴은 `코드 중복`과 `직접적인 의존`을 해결하는데 도움을 주는 패턴이다.
- 예를 들어서 아래와 같은 코드가 있다고 가정해볼 때 Notebook과 Desktop은 Power, Memory, Cpu에 의존하고 있다.

  <details>
  <summary>패턴 적용 전</summary>

  <div markdown="1">

  ```kotlin
  class ModelAPower {
    fun turnOn() { }
  }
  
  class Memory {
      fun load() { }
  }
  
  class Cpu {
      fun execute() { }
  }
  
  class Notebook {
      private val modelAPower = ModelAPower()
      private val memory = Memory()
      private val cpu = Cpu()
  
      fun start() {
          modelAPower.turnOn()
          memory.load()
          cpu.execute()
      }
  }
  
  class Desktop {
      private val modelAPower = ModelAPower()
      private val memory = Memory()
      private val cpu = Cpu()
  
      fun start() {
          modelAPower.turnOn()
          memory.load()
          cpu.execute()
      }
  }
  ```

  </div>
  </details>
<br>

- 위 코드는 몇가지 문제가 있는데 하나씩 살펴보면 아래와 같다.
  - 동일한 코드가 중복된다. (turnOn, load, execute, 변수 선언)
  - 실행의 순서가 power, memory, cpu에서 power ,cpu, memory로 바뀐다면 Notebook과 Desktop의 코드가 변경되어야 한다.
  - power가 ModelA에서 modelB로 바뀔 경우에도 Notebook과 Desktop의 코드가 변경되어야 한다.

- 위 문제는 Notebook과 Desktop이 power, memory, cpu를 의존하고 있기 때문에 발생하는 문제인데, 파사드 패턴은 코드의 중복과 의존성을 줄여주기 위한 디자인 패턴이다.
- 아래는 파사드 패턴을 적용했을 때의 결과이다.

  <details>
  <summary>패턴 적용 후</summary>

  <div markdown="1">

  ```kotlin
  package facade_pattern
  
  class ModelAPower {
      fun turnOn() { }
  }
  
  class Memory {
      fun load() { }
  }
  
  class Cpu {
      fun execute() { }
  }
  
  class Computer {
      private val modelAPower = ModelAPower()
      private val memory = Memory()
      private val cpu = Cpu()
  
      fun start() {
          modelAPower.turnOn()
          memory.load()
          cpu.execute()
      }
  }
  
  class Notebook {
      private val computer = Computer()
      fun start() {
          computer.start()
      }
  }
  
  class Desktop {
      private val computer = Computer()
      fun start() {
          computer.start()
      }
  }
  ```

  </div>
  </details>
<br>

  - 파사드 패턴을 적용함으로써 클라이언트 (Notebook, Desktop)와 서브 시스템 (Power, Cpu, Memory)간의 직접적인 의존을 제거했으며 코드의 중복 또한 줄어들게 되었다.

### 장점과 특징

  - 파사드를 정의함으로써 클라이언트 변경 없이 서브 시스템을 변경할 수 있다는 장점이 있다.
  - 단지 여러 클라이언트의 중복된 코드들을 파사드로 추상화하여 사용했을 뿐, 서브 시스템에 대한 직접적인 접근을 막는 것은 아니다.
    - 필요한 경우 클라이언트에서 서브 시스템을 바로 사용할 수 있다.
  - 퍼사드는 공통적인 작업에 대해 간편한 메소드들을 제공해준다.
  - 좋게 작성되지 않은 API의 집합을 하나의 좋게 작성된 API로 감싸준다.
  - 시스템의 복잡성을 숨기고 클라이언트에 더 간단한 인터페이스를 제공한다.
    - 예를 들어서 적용 전 코드에서 Notebook과 Desktop의 start 메소드가 훨씬 더 복잡한 로직을 가지고 있을 경우 구현 도중에 코드를 누락하거나 잘못 작성할 수 있는 가능성이 높다.
    - 따라서 파사드 패턴을 적용함으로써 클라이언트는 어떻게 구현해야 되는지에 대한것은 알 필요가 없으므로 더 간단하게 코드를 사용할 수 있게 된다.
  - 인터페이스와 같이 사용하여 서브 시스템의 교체를 쉽게 할 수도 있다.

### 인터페이스와 비교

  - 인터페이스는 공통된 메소드들을 정의해 내부 코드를 감춤으로써 확장과 변경에 초점이 맞춰져있지만 파사드 패턴은 내부 코드를 감추는 것은 동일하지만 단순히 공통적으로 사용되는 클래스와 메소드들을 하나의 파사드 클래스 내부 메소드 안에서 묶어주는 역할을 한다.
  - 파사드 패턴은 사용하는 클래스와 의존 관계에 있다.

### 어댑터 패턴과 비교

  - 어댑터 패턴은 단순히 호환되지 않는 두 개의 인터페이스를 연결해주는 패턴이다. 
    - 즉, DVI to HDMI 케이블같은 것을 의미한다.
  - 그러나 파사드 패턴은 기가지니처럼 어떤 행동 하나에 티비, 전등, 가스 등을 제어하는 것과 동일하게 이해할 수 있다.
  - [참고](https://stackoverflow.com/questions/2961307/what-is-the-difference-between-the-facade-and-adapter-pattern)

---

## 추상 팩토리 패턴

- 어떤 조건별로 다른 객체들을 생성해야할 때 추상 팩토리 패턴을 사용할 수 있다.
- 객체의 생성 책임을 분리한다.

<details>
<summary>그릴 객체 추상/콘크리트 클래스</summary>

<div markdown="1">

```kotlin
abstract class DrawObject {
    abstract var point: Point
    abstract var size: Size

    abstract fun draw()
}

class Image(override var point: Point, override var size: Size) : DrawObject() {
    override fun draw() {
        println("Image -> point: ${point}, size: $size")
    }
}

class Rectangle(override var point: Point, override var size: Size) : DrawObject() {
    override fun draw() {
        println("Rectangle -> point: ${point}, size: $size")
    }
}
```

</div>
</details>

<details>
<summary>그릴 객체 팩토리 클래스 구현</summary>

<div markdown="1">

```kotlin
abstract class DrawObjectFactory {

    companion object {
        fun getFactory(index: Int): DrawObjectFactory {
            return when (index) {
                1 -> RectangleFactory()
                else -> ImageFactory()
            }
        }
    }

    abstract fun createDrawObject(point: Point, size: Size): DrawObject
}
```

</div>
</details>

<details>
<summary>사각형, 이미지 팩토리 클래스 구현</summary>

<div markdown="1">

```kotlin
class ImageFactory : DrawObjectFactory() {
    override fun createDrawObject(point: Point, size: Size): DrawObject {
        return Image(Point(11, 22), Size(33, 44))
    }
}

class RectangleFactory : DrawObjectFactory() {
    override fun createDrawObject(point: Point, size: Size): DrawObject {
        return Rectangle(Point(10, 20), Size(30, 40))
    }
}
```

</div>
</details>

<details>
<summary>메인 구현</summary>

<div markdown="1">

```kotlin
fun main() {
    // 사각형
    var drawObjectFactory = DrawObjectFactory.getFactory(1)
    var drawObject = drawObjectFactory.createDrawObject(Point(10, 20), Size(30, 40))
    drawObject.draw()

    // 이미지
    drawObjectFactory = DrawObjectFactory.getFactory(2)
    drawObject = drawObjectFactory.createDrawObject(Point(11, 22), Size(33, 44))
    drawObject.draw()
}
```

</div>
</details>

<details>
<summary>결과</summary>

<div markdown="1">

```text
Rectangle -> point: Point(x=10, y=20), size: Size(width=30, height=40)
Image -> point: Point(x=11, y=22), size: Size(width=33, height=44)
```

</div>
</details>


![팩토리](https://user-images.githubusercontent.com/29175138/159114789-ff10cf26-845f-4164-9c7e-aa134d982897.png)


### 장점

- 클라이언트 변경 없이 사용할 객체를 동적으로 교체할 수 있다.
- 객체의 생성을 분리함으로써 메인에서는 생성에 대한 책임없이 사용하기만 하면 된다.

---

## 컴포지트 패턴

- 클라이언트가 복합 객체(group)나 단일 객체를 동일하게 취급하는 것으로 전체-부분을 구성하는 클래스가 동일 인터페이스를 구현하도록 하는 디자인 패턴이다.
- 두꺼비집으로 생각하면 쉬울 것 같다.

```kotlin
interface Device {
    fun turnOn()
    fun turnOff()
}

class Computer : Device {
    override fun turnOn() { }
    override fun turnOff() { }
}

class Light : Device {
    override fun turnOn() { }
    override fun turnOff() { }
}

class InternetCafe : Device {
    private val deviceList = mutableListOf<Device>()

    override fun turnOn() {
        deviceList.forEach {
            it.turnOn()
        }
    }

    override fun turnOff() {
        deviceList.forEach {
            it.turnOff()
        }
    }

    fun addDevice(device: Device) {
        deviceList.add(device)
    }
}

fun main() {
    val internetCafe = InternetCafe()
    internetCafe.addDevice(Computer())
    internetCafe.addDevice(Computer())

    internetCafe.addDevice(Light())
    internetCafe.addDevice(Light())

    internetCafe.turnOff()
}
```

### 장점

- 클라이언트가 컴포지트(그룹)과 컴포넌트(단일 객체)를 구분하지 않고 컴포넌트 인터페이스만으로 프로그래밍 할 수 있다.

---

