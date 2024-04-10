## 1. AQS và handle process synchronized bằng tay

AQS (AbstractQueuedSynchronizer) là một kiến trúc đồng bộ hoá trong Java thường được sử dụng để xây dụng các loại cơ chế đồng bộ tuỳ chỉnh. Trong việc thiết kế một cơ chế đồng bộ tuỳ chỉnh dựa trên AQS, thường sẽ thực hiện qua 2 bước sau:

- Kế thừa và ghi đè: người sử dụng sẽ kế thừa Class `AbstractQueuedSynchronizer` và ghi đè các phương thức cụ thể cần thiết cho cơ chế đồng bộ.
- Sử dụng AQS trong cài đặt đồng bộ tuỳ chỉnh: AQS được tích hợp vào cơ chế đồng bộ tuỳ chỉnh của người dùng và gọi các phương thức mẫu của nó. Các phương thức mẫu này sẽ gọi phương thức được ghi đè bởi người sử dụng.

Trong phương thức mẫu, có một khái niệm quan trọng còn được gọi là `phương thức khuyết` (hook method). Đây là các phương thức trong một Class trừu tượng, thường được đánh dấu bằng `protected` có thể có một triển khai hoặc method rỗng. Nội dung logic của các phương thức này được thực hiện bởi lớp con. Tại sao chúng ta ko sử dụng phương thức trừu tượng? bởi vì phương thức trừu tượng yêu cầu tất cả các lớp con phải triển khai nó, điều này có thể dẫn đến sự lặp lại mã lớn.

Với kiến thức lý thuyết này, chúng ta có thể xem xét các công cụ đồng bộ được cài đặt dựa trên AQS trong Java.

### 1.1 Semaphore

Semaphore là một cơ chế trong java được sử dụng để kiểm soát số lượng luồng được phép truy cập vào một tài nguyên cụ thể trong cùng một lúc. Khác với `Synchronized` và `ReentrantLock` chỉ cho phép duy nhất một luồng được phép truy cập vào tài nguyên trong cùng một thời điểm, Semaphore cho phép nhiều luồng truy cập vào tài nguyên, nhưng tối đa là một số cố định được xác định trước.

```java
public class Test {
    private final Semaphore semaphore;
	// Constructor method initializes the semaphore
    public Test(int limit) {
        this.semaphore = new Semaphore(limit);
    }

    public void useResource() {
        try {
            semaphore.acquire();
            // Use resources
            System.out.println("资源use:" + Thread.currentThread().getName());
            Thread.sleep(1000); // Simulate resource usage time
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            semaphore.release();
            System.out.println("资源release:" + Thread.currentThread().getName());
        }
    }

    public static void main(String[] args) {
        // Limit 3 threads to access resources at the same time
        Test pool = new Test(3);

        for (int i = 0; i < 4; i++) {
            new Thread(pool::useResource).start();
        }
    }
}
```

> Thông qua ví dụ này, chúng ta đã thành công trong việc giới hạn số lượng Thread có thể truy cập vào tài nguyên trong một thời điểm không vượt quá 3. Trong đó 2 phương thức chính được sử dụng là `acquire()` , `release()`.
>
> - `acquire()` : lấy quyền truy cập.
>
> Khi gọi method này, nó gọi đến một phương thức trong AQS có tên `acquireSharedInterruptibly(int)`. Phương thức này gọi tiếp vào method `tryAcquireShared(arg)` . Phương thức này được implement trong 2 Class static bên trong Class Semaphore có tên `FairSync ` (chế độ công bằng) và `NonfairSync ` (chế độ không công bằng)
>
> - `release()` : giải phóng tài nguyên
>
> Tương tự, khi gọi phương thức này, nó sử dụng `releaseShared()` trong AQS, và trong phương thức này cũng sử dụng một phương thức là `tryReleaseShared(int arg)`, nguyên lý là giống như trên.
>
> 【Bổ sung】 Ngoài Semaphore, trong AQS còn có một lớp tên là Sync, cung cấp `nonfairTryAcquireShared()` để thử lấy quyền truy cập theo cách quay vòng và `tryReleaseShared(int releases)` để thử giải phóng quyền truy cập. Ngoài Semaphore, còn có CountDownLatch (đếm ngược) và CyclicBarrier (rào cản vòng lặp) cũng được cài đặt dựa trên AQS. 
>
> ```java 
> abstract static class Sync extends AbstractQueuedSynchronizer {
>     private static final long serialVersionUID = 1192457210091910933L;
> 
>     Sync(int permits) {
>         setState(permits);
>     }
> 
>     final int getPermits() {
>         return getState();
>     }
> 
>     final int nonfairTryAcquireShared(int acquires) {
>         for (;;) {
>             int available = getState();
>             int remaining = available - acquires;
>             if (remaining < 0 ||
>                 compareAndSetState(available, remaining))
>                 return remaining;
>         }
>     }
> 
>     protected final boolean tryReleaseShared(int releases) {
>         for (;;) {
>             int current = getState();
>             int next = current + releases;
>             if (next < current) // overflow
>                 throw new Error("Maximum permit count exceeded");
>             if (compareAndSetState(current, next))
>                 return true;
>         }
>     }
> 
>     final void reducePermits(int reductions) {
>         for (;;) {
>             int current = getState();
>             int next = current - reductions;
>             if (next > current) // underflow
>                 throw new Error("Permit count underflow");
>             if (compareAndSetState(current, next))
>                 return;
>         }
>     }
> 
>     final int drainPermits() {
>         for (;;) {
>             int current = getState();
>             if (current == 0 || compareAndSetState(current, 0))
>                 return current;
>         }
>     }
> }
> public void acquire() throws InterruptedException {
>     sync.acquireSharedInterruptibly(1);
> }
> ```
>
> ```java
> public final void acquireSharedInterruptibly(int arg)
>     throws InterruptedException {
>     if (Thread.interrupted() ||
>         (tryAcquireShared(arg) < 0 &&
>          acquire(null, arg, true, true, false, 0L) < 0))
>         throw new InterruptedException();
> }
> ```

```java
import java.util.concurrent.Semaphore;

public class SemaphoreExample {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3); // 1
        for (int i = 0; i < 5; i++) {
            Thread thread = new Thread(new MyThread(semaphore, "Thread-" + i));
            thread.start();
        }
    }

    static class MyThread implements Runnable {
        private final Semaphore semaphore;
        private final String name;

        public MyThread(Semaphore semaphore, String name) {
            this.semaphore = semaphore;
            this.name = name;
        }

        @Override
        public void run() {
            try {
                System.out.println(name + " is waiting to acquire lock.");
                semaphore.acquire(); // 2
                System.out.println(name + " has acquired lock.");
                Thread.sleep(2000);
                System.out.println(name + " is releasing lock.");
                semaphore.release(); // 3
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

Trong ví dụ trên Semaphore được tạo với giá trị ban đầu là 3, có nghĩa là chỉ có tối đa 3 thread được phép truy cập vào tài nguyên cùng thời điểm. Khi một Thread khác muốn truy cập vào tài nguyên, nó phải gọi phương thức `acquire()` để yêu cầu quyền truy cập. Sau khi hoàn thành công việc Thread đang giữ `permit` cần phải gọi `release()` để giải phóng tài nguyên mà Thread đang thực thi, để có thể cho phép các Thread khác truy cập vào tài nguyên. 

> //1 chúng ta đang khởi tạo một đối tượng Semaphore với một số lượng phần từ cấp phát ban đầu là 3. Trong `Semaphore` số này đại diện cho số lượng `permit` (quyền truy cập) được cấp phát ban đầu cho các luồng. Điều này có ý nghĩa là tối đa chỉ có 3 luồng được phép đồng thời truy cập vào tài nguyên được bảo vệ với Semaphore trước khi cần phải chờ đợi một luồng khác giải phòng một `permit` trước đó.
>
> //2 Đợi để giành quyền sử dụng tài nguyên
>
> //3 Thả lock

```java
//exclusive way. Try to obtain the resource, returning true if successful and false if failed.
protected boolean tryAcquire(int)
//exclusive way. Try to release the resource, returning true if successful and false if failed.
protected boolean tryRelease(int)
//Sharing method. Try to get resources. A negative number indicates failure; 0 indicates success, but there are no remaining available resources; a positive number indicates success, and there are remaining resources.
protected int tryAcquireShared(int)
//Sharing method. Try to release the resource, returning true if successful and false if failed.
protected boolean tryReleaseShared(int)
//Whether this thread is monopolizing resources. Only when condition is used do you need to implement it.
protected boolean isHeldExclusively()
```

---

Dưới đây là cách triển khai một khóa mutex (khóa độc quyền) dựa trên AbstractQueuedSynchronizer (AQS), nơi chỉ một luồng được phép truy cập vào tài nguyên tại một thời điểm.

Bước 1: Trước hết, chúng ta sẽ định nghĩa một lớp khóa mutex có tên là OnlySyncByAQS. Trong lớp này, chúng ta sẽ viết một lớp nội tĩnh để kế thừa AbstractQueuedSynchronizer. Trong lớp nội tĩnh này, chúng ta sẽ ghi đè phương thức tryAcquire() của AQS để thử lấy tài nguyên theo cách độc quyền; và ghi đè phương thức tryRelease() để thử giải phóng tài nguyên. Đây là hai phương thức chính! Sau đó, chúng ta sẽ đóng gói nó thành các phương thức khóa (lock()) và mở khóa (unlock()), và trong đó chúng ta sẽ gọi phương thức acquire() và release() của AQS thông qua mẫu phương thức, để truy cập vào các phương thức mẫu của chúng tôi.

```java
public class OnlySyncByAQS {
    private final Sync sync = new Sync();

	//Obtain permission and lock resources
    public void lock() {
        sync.acquire(1);
    }

    //Release license, unlock
    public void unlock() {
        sync.release(1);
    }

    //Determine whether it is exclusive
    public boolean isLocked() {
        return sync.isHeldExclusively();
    }

    //Static inner class, inherit AQS, override hook method
    private static class Sync extends AbstractQueuedSynchronizer {

        //Rewrite the tryAcquire method of AQS in exclusive mode to try to obtain resources.
        @Override
        protected boolean tryAcquire(int arg) {
            //CAS attempts to change status
            if (compareAndSetState(0, 1)) {
                //In exclusive mode, set the lock holder to the current thread, from AOS
                setExclusiveOwnerThread(Thread.currentThread());
                System.out.println(Thread.currentThread().getName()+"获取锁成功");
                return true;
            }
            System.out.println(Thread.currentThread().getName()+"获取锁失败");
            return false;
        }

        //exclusive way. Try to release the resource, returning true if successful and false if failed.
        @Override
        protected boolean tryRelease(int arg) {
            if (getState() == 0) {
                throw new IllegalMonitorStateException();
            }
            //The holder of the null lock
            setExclusiveOwnerThread(null);
            //Change the status to 0, unlocked status
            setState(0);
            System.out.println(Thread.currentThread().getName()+"Lock released successfully！");
            return true;
        }

        //Determine whether the thread is monopolizing resources and return state=1
        @Override
        protected boolean isHeldExclusively() {
            return getState() == 1;
        }
    }
}
```

Bước 2: Ở bước này, chúng tôi viết một lớp kiểm tra để gọi khóa mutex tùy chỉnh này.

```java
public class Test {

    private OnlySyncByAQS onlySyncByAQS = new OnlySyncByAQS();

    public void use(){
        onlySyncByAQS.lock();
        try {
            //Sleep for 1 second to obtain shared resources
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            onlySyncByAQS.unlock();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Test test = new Test();
        //Multiple threads compete for resources, and only one thread gets the lock at a time
        for (int i = 0; i < 3; i++) {
            new Thread(()->{
                test.use();
            }).start();
        }
    }
}
```

