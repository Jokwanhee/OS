# Daemon Thread
[참고 자료](https://medium.com/@lunay0ung/thread-daemon-thread-3b89ce606b04)
```
🚀 스레드에는 Daemon Thread 와 Normal Thread 로 나뉜다. Daemon Thread 의 개념은 Normal Thread 의 작업을 돕는 보조적 역할을 
담당하는 쓰레드이다. Daemon Thread 는 가비지 컬렉션, 요청 처리, 리소스 청소 와 같은 백그라운드 task 를 실행하며 낮은 우선순위 (low priority) 
를 가지고 있다. 그리고 Normal Thread 가 종료되면 Daemon Thread 도 강제 종료된다.
```

`예제`
```kotiln
class GoniThread(
):Thread(){
    override fun run() {
        if (currentThread().isDaemon){
            println("Daemon Thread running")
        } else {
            println("Normal Thread running")
        }
    }
}

fun main() {
    val goniThread = GoniThread()
    val goniThreadDaemon = GoniThread()

    goniThreadDaemon.isDaemon = true

    goniThread.start()
    goniThreadDaemon.start()
}

>>>
Daemon Thread running
Normal Thread running
```
`에러 : IllegalThreadStateException`
```
❗start() 호출 이후에 isDaemon 설정을 바꾼다면 에러가 발생한다.
```
```kotlin
goniThread.start()
goniThreadDaemon.start()

goniThreadDaemon.isDaemon = true    
```
`Normal Thread 종료 시 Daemone Thread 종료 테스트`
```kotiln
class GoniThread(
):Thread(){
    override fun run() {
        if (currentThread().isDaemon){
            while (true){
                println("Daemon Thread running")
                sleep(1000)
            }
        } else {
            while (true){
                try {
                    println("Normal Thread running")
                    sleep(1000)
                } catch (e: InterruptedException){
                    println("Normal Thread exit")
                    println("isDaemon : ${currentThread().isDaemon}")
                    break
                }
            }
        }
    }
}

fun main() {
    val goniThread = GoniThread()
    val goniThreadDaemon = GoniThread()

    goniThreadDaemon.isDaemon = true

    goniThread.start()
    goniThreadDaemon.start()
    sleep(2000)
    goniThread.interrupt()
}

>>>
Normal Thread running
Daemon Thread running
Daemon Thread running
Normal Thread running
Normal Thread exit
isDaemon : false
```