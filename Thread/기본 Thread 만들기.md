# Thread
## ✌ Kotlin 언어를 이용한 Thread 프로그램 만들기
## ✍ Thread 클래스 구현
```
🚀 Thread() 클래스를 사용하여 스레드를 사용할 수 있다.
```
```kotlin
class GoniThread: Thread(){
    override fun run() {
        println("${currentThread()}  : it's GoniThread run")
    }
}

fun main() {
    println("${Thread.currentThread()} : run the Thread in main function")

    val goniThread = GoniThread()
    goniThread.start()

    val goniThread2 = GoniThread()
    goniThread2.start()

    val goniThread3 = GoniThread()
    goniThread3.start()
}
```
```text
>>> 
Thread[main,5,main] : run the Thread in main function
Thread[Thread-0,5,main]  : it's GoniThread run
Thread[Thread-1,5,main]  : it's GoniThread run
Thread[Thread-2,5,main]  : it's GoniThread run
```
---
**run()**   
`currentThread() 를 확인하니 name 이 main 으로 바뀌고 GoniThread 클래스에서 run 함수는 호출된다. 하지만 현재 run()메서드는 start() 메서드로 대체(replace)되었다.`
```text
Thread.run()

val goniThread = GoniThread()
goniThread.run()

>>>
Thread[main,5,main]
```
---
**start()**   
`start() 메서드 호출 시 Thread 클래스에서 run() 함수가 호출됨을 알 수 있다.`
```
Thread.start()
```
---
**join()**   
`join() 메서드는 Thread 가 종료될 때까지 기다린다.`   
**join() 미사용**   
```kotlin
class GoniThread: Thread(){
    override fun run() {
        super.run()
        println("${currentThread()}  : it's GoniThread run")
        println("name : jo")
        println("age : 12")
    }

}

fun main() {
    println("goniThread start")

    val goniThread = GoniThread()
    goniThread.start()

    println("goniThread end")
}

>>>
goniThread start
goniThread end
Thread[Thread-0,5,main]  : it's GoniThread run
name : jo
age : 12
```
**join() 사용**
```kotlin
class GoniThread: Thread(){
    override fun run() {
        super.run()
        println("${currentThread()}  : it's GoniThread run")
        println("name : jo")
        println("age : 12")
    }

}

fun main() {
    println("goniThread start")
    
    val goniThread = GoniThread()
    goniThread.start()
    goniThread.join()

    println("goniThread end")
}


>>>
goniThread3 start
Thread[Thread-0,5,main]  : it's GoniThread run
name : jo
age : 12
goniThread3 end
```
---
**currentThread()**   
`Thread 클래스 내 run 오버라이드 함수에서 currentThread() 를 호출하면 Thread 이름, 우선순위를 알 수 있다.`
```text
>>> Thread[main,5,main]
        name/priority/main
        (3번째는 main 고정)
```
---
## ✍ Runnable 구현
```
🚀 Runnable 인터페이스를 사용하여 run() 함수를 오버라이딩 할 수 있다. 인터페이스를 사용하기 때문에 꼭 run() 함수를 재정의 해주어야 한다. Thread() 클래스 인자로 Runnable 객체를 넣어주면된다.
```
```kotlin
class GoniRunnable: Runnable{
    override fun run() {
        println("GoniRunnable run()")
    }
}

fun main() {
    println("goniThread Start")
    val goniThread = Thread(GoniRunnable())
    goniThread.start()
    goniThread.join()
    println("goniThread End")
}

>>>
goniThread Start
GoniRunnable run()
goniThread End
```
## ✍ Runnable 람다식 구현
```
🚀 Runnable 인터페이스를 상속하는 별도의 클래스를 정의하는 것 말고 람다 표현식으로 간단하게 구현 가능하다.
```
`스레드 join() 을 사용하지 않으면 간단하게 Thread 클래스를 람다 표현식으로 나타낼 수 있다.`
```kotlin
fun main() {
    println("goniThread Start")

    Thread {
        println("GoniThread syntax")
    }.start()

    println("goniThread End")
}

>>>
goniThread Start
goniThread End
GoniThread syntax
```
## ✍ Kotlin thread() 함수
```
🚀 코를린에서는 thread() 함수를 제공하며 thread(){} 구문을 사용하여 진행할 수 있다.
```
`thread()`   
[thread()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.concurrent/thread.html)
```
fun thread(
    start: Boolean = true,
    isDaemon: Boolean = false,
    contextClassLoader: ClassLoader? = null,
    name: String? = null,
    priority: Int = -1,
    block: () -> Unit
): Thread
```
```kotlin
fun main() {
    println("goniThread Start")

    thread {
        println("GoniThread syntax")
    } // .start() 메서드를 사용하지 않아도 된다.
      // 이유는 기본적으로 thread(start=true) default
      // 스레드 실행을 원치 않으면 인자 start = false 넣어주면된다.
      // .join() 사용가능  
    

    println("goniThread End")
}

>>>
goniThread Start
goniThread End
GoniThread syntax
```

```
🚀isDaemon 은 무엇인가?
코틀린 문서에서는 isDaemon = true 이면, 스레드는 데몬 스레드 로 만들어진다. 실행 중인 유일한 스레드가 모두 데몬 스레드인 경우 JVM 이 종료된다.
```
---
## 스레드 종료
```
🚀 stop() 메서드는 deprecated 되었다.
```
### Flag 사용하기
```
🚀 Thread 클래스에 인자로 flag를 default 로 false 상태로 저장한 뒤 외부에서 true 값을 던지면 해당 스레드 종료하는 조건을 만들어준다.
```
```kotlin
class GoniThread(
    var stopFlag : Boolean = false
):Thread(){
    override fun run() {
        while (!stopFlag){
            println("GoniThread running...")
            sleep(2000)
//          내부에서 stopFlag true 설정
//          stopFlag = true
        }
        println("GoniThread exit")
    }
}

fun main() {
    var goniThread = GoniThread()
    println("goniThread Start")

    goniThread.start()

    sleep(5000)
//          외부에서 stopFlag true 설정
    goniThread.stopFlag = true

    println("goniThread End")
}

>>>
goniThread Start
GoniThread running...
GoniThread running...
GoniThread running...
goniThread End
GoniThread exit
```

### interrupt() 메서드 사용하기
`Thread.interrupt()`
```
🚀 interrupt() 를 사용하면 Thread 를 외부에서 안전하게 종료시킬 수 있다. 주의할 점은 강제종료 시키는 방법이 아닌 sleep() 메서드가 호출 시 InterruptedException 오류를 발생시켜 스레드를 종료하는 방법이다. 한마디로 run() 메서드 내에서 일시정지되는 이벤트가 발생해야 한다.
```
```kotlin
class GoniThread(
):Thread(){
    override fun run() {
        while (true){
            try {
                println("GoniThread running...")
                sleep(1000)
            } catch (e: InterruptedException){
                println("Interrupted start!")
                println("GoniThread exit")
                break
            }
        }
    }
}

fun main() {
    var goniThread = GoniThread()
    println("goniThread Start")

    goniThread.start()
    sleep(2000)
    goniThread.interrupt()

    println("goniThread End")
}

>>>
goniThread Start
GoniThread running...
GoniThread running...
goniThread End
GoniThread running...
Interrupted start!
GoniThread exit
```

`isInterrupted`
```kotlin
class GoniThread(
):Thread(){
    override fun run() {
        println("isInterrupted f : $isInterrupted")
        while (true){
            if (isInterrupted){
                println("isInterrupted t : $isInterrupted")

                println("Interrupted start!")
                println("GoniThread exit")
                break
            }
            println("GoniThread running...")
        }
    }
}

fun main() {
    var goniThread = GoniThread()
    println("goniThread Start")

    goniThread.start()
    sleep(1) // 0.001 sec
    goniThread.interrupt()

    println("goniThread End")
}

>>>
goniThread Start
isInterrupted f : false
GoniThread running...
GoniThread running...
...
GoniThread running...
GoniThread running...
goniThread End
isInterrupted t : true
Interrupted start!
GoniThread exit
```