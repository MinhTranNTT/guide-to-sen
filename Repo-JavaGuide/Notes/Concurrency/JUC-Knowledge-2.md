## 1. Synchronized keyword

### 1.1 Synchronized code block

- Synchronized là từ khoá trong java được sử dụng để tạo ra các khối đồng bộ hoá, có tác dụng trong việc bảo vệ an toàn của luồng (thread safety).

- Synchronized scope: synchronized có thể được sử dụng để đồng bộ hoá một khối mã (code block), khi đó khối mã được gọi là "khối lệnh đồng bộ" synchronized block.

- Phạm vi của synchronized được xác định bởi dấu {} mà nó bao quanh. Mã nằm trong các dấu {} này sẽ được đồng bộ hoá.
- Đối tượng mà synchronized tác động đến: khi sử dụng synchronized để đồng bộ hoá một khối mã, đối tượng mà nó tác động đến là đối tượng mà khối mã đó thuộc về. Nói cách khác chỉ có một luồng được phép thực hiện khối mã bên trong khối lệnh synchronized.

Tóm lại synchronized được sử dụng để đồng bộ hoá các khối mã trong JAVA, có tác dụng bảo vệ sự an toàn của Thread và chỉ cho phép một Thread truy cập vào các khối mã được đồng bộ tại một thời điểm.

```java
synchronized (this){
	System.out.println("synchronized code block");
}
```

### 1.2 Synchronized method

synchronized nằm trên method được gọi là method synchronized. phạm vi là toàn bộ phương thức.

```java
public synchronized void sale() {   
}
```

*synchronized keyword không thể được kế thừa. Nếu một method trong class Parent sử dụng từ từ khoá synchronized, các lớp con ko tự động kế thừa method đó.

Nếu lớp con Override method đó mà ko có từ khoá synchronized method này sẽ ko được đồng bộ hoá. Tuy nhiên nếu lớp con ghi đè một method synchronized từ lớp cha và có keyword synchronized, thì method trong lớp con cũng sẽ được đồng bộ hoá. 

### 1.3 Synchronized static method

Synchronized một phương thức tĩnh, phạm vi của nó là toàn bộ phương thức tĩnh và mục tiêu của nó là tất cả các đối tượng của lớp này.

```java
public static synchronized void test(){
}
```

### 1.4 Synchronized Class

Khi synchronized một lớp, phạm vi của nó là phần được đặt trong ngoặc đơn sau khi được đồng bộ hóa và phạm vi của nó là tất cả các đối tượng của lớp này.

```java
class Ticket {
    public void sale() {
        synchronized (Ticket.class) {

        }
    }
}
```

## 2. Test

sử dụng synchronized cho bài toán bán vé trong môi trường đa luồng

```java
package com.org.keywords.synchronization;

public class SynchronizedDemo {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();
        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        }, "A").start();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        }, "B").start();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        }, "C").start();
    }
}

class Ticket {
    private int number = 50;

    public synchronized void sale() {
        if (number > 0) {
            System.out.println(Thread.currentThread().getName() + " : " + (number--) + " " + number);
        }
    }
}
```

## 3. Reviews

Hiệu suất của synchronized rất thấp, bởi vì nếu có một khối mã nhất định được triển khai với synchronized, khi một Thread đi vào khối mã, các Thread khác chỉ có thể đợi luồng lấy khoá và giải phóng trước khi có Thread kế tiếp đi vào khối mã. Chỉ có 2 tình huống để Thread có thể nhận được khoá giải phóng khoá.

- Thread phải hoàn thành thực hiện bình thường và sau đó nhả khoá.
- Trong quá trình thực thi, một ngoại lệ xảy ra và JVM cho phép luồng tự động giải phóng khoá.

Sau đó, hãy tưởng tượng một Thread khác lấy được khoá và bị chặn do chờ IO hoặc lý do khác, nhưng không thể mở khoá. Các Thread vẫn đang đợi khoá để vào khối mã. 

Vì vậy, rất cần thiết phải có cơ chế ngăn chặn Thread chờ đợi vô thời hạn, chẳng hạn có thể thay đổi để chờ một khoảng thời gian hoặc phản hồi khi có ngắt? Chúng ta có thể làm điều này thông qua Lock.  

### 4. Nói chuyện với chính mình

Những kiến thức về Lock sẽ có ở bài viết tiếp theo.
Gần đây tôi đã bắt đầu nghiên cứu lại JUC và tôi cảm thấy thực sự có rất nhiều nội dung Java, nhưng để tiến xa hơn, tôi vẫn cảm thấy mình cần phải đặt nền tảng vững chắc.
Xin chào, tôi là Minh Tran, tôi là một hạt giống nhỏ trên con đường học Java, tôi cũng mong một ngày nào đó nó sẽ bén rễ và phát triển thành một cây lớn.
Mong được các bạn cùng nhau động viên😁
Khi chúng ta gặp lại nhau, chúng ta đã hoàn thành được điều gì đó.

## 5. Lock

việc triển khai Lock cung cấp phạm vi hoạt động khoá rộng hơn mức có thể đạt được bằng cách sử dụng các phương thức và câu lệnh được đồng bộ hoá.

Trong java, có một số loại khoá (Lock) khác nhau được sử dụng để quản lý đồng bộ hoá truy cập vào tài nguyên chia sẽ giữa các Thread. Dưới đây là giải thích về một số loại khoá phổ biến.

-  Khoá có thể lặp lại (Reentrant Lock)

khoá có thể lặp lại cho phép 1 Thread có thể một cách an toàn gọi lại một phương thức hoặc khối mã đã được khoá mà không bị chặn bởi chính bản thân nó.

Điều này có nghĩa là khi một Thread đã nắm giữ một khoá, nó có thể tiếp tục lấy mà không bị chặn.

- Khoá công bằng (Fair Lock)

Khoá công bằng đảm bảo rằng các Thread sẽ được cấp quyền truy cập vào tài nguyên theo thứ tự hợp lý, dựa trên thời gian hàng đợi của chúng. Thread đợi lâu hơn sẽ được ưu tiên trước trong việc cấp quyền truy cập vào tài nguyên.

- Khoá có thể ngắt (Interruptible Lock)

Khoá có thể ngắt cho phép một Thread chờ đợi có thể bị ngắt (interrupted) và thoát khỏi trạng thái chờ đợi khi một Thread khác gọi phương thức interrupt().

Điều này hữu ích khi cần huỷ bỏ một hoạt động đang chờ đợi để có thể tiếp tục xử lý các yêu cầu khác.

-  Khoá đọc ghi (Read-Write Lock)

khoá đọc, ghi chia tài nguyên thành 2 phần: một phần cho việc đọc và một phần cho việc ghi. Khi có nhiều Thread muốn đọc dữ liệu, chúng có thể được phép đọc cùng một lúc mà ko gây ra xung đột.

Khi một luồng muốn ghi dữ liệu, nó phải chờ đợi để lấy khóa ghi và không cho phép bất kỳ luồng đọc nào khác đọc dữ liệu cho đến khi ghi được hoàn thành.

```java
public interface Lock {

    void lock(); //Get the lock.

    /**
    Acquire the lock unless the current thread is interrupted.
    
If available, acquire the lock and return immediately.
If the lock is not available, the current thread is disabled for thread scheduling purposes and sleeps until one of the following two things occurs:
The lock is acquired by the current thread;
Or some other thread interrupts the current thread and supports interrupt acquisition lock.
If current thread:
Set its interrupt status when entering this method;
Either interrupt when acquiring the lock, support interrupt acquisition of the lock,
    */
    void lockInterruptibly() throws InterruptedException; 

    /**
    The lock is only acquired when idle at the time of the call.
If available, acquires the lock and returns true immediately. If the lock is not available, this method will immediately return a false value.
	*/
    boolean tryLock();
    
    //One more waiting time than above 
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;  

   	// Unlock
    void unlock(); 
    
    //Returns a new Condition instance bound to this Lock instance.
    Condition newCondition();  。
}
```

## 6. Một số phương pháp phổ biến

lock() là một trong những phương thức được sử dụng phổ biến nhất. Chức năng của nó là lấy khóa. Nếu khóa đã được các luồng khác lấy được thì luồng hiện tại sẽ bị vô hiệu hóa để lập lịch trình luồng và sẽ ở trạng thái không hoạt động, đợi cho đến khi khóa được mua lại.
Nếu sử dụng khóa thì khóa phải được chủ động nhả, kể cả khi có ngoại lệ xảy ra thì chúng ta vẫn cần phải chủ động nhả khóa, vì khóa sẽ không tự động được nhả như đã đồng bộ. Do đó, nếu bạn sử dụng khóa, bạn phải thực hiện việc đó trong try{}catch(){} và đặt mã để giải phóng khóa vào cuối cùng{} để đảm bảo rằng khóa sẽ được giải phóng nhằm ngăn chặn xảy ra bế tắc.
Chức năng của unlock() là chủ động mở khóa.
Có một số lớp triển khai cho loại giao diện khóa. Đây là một lớp ngẫu nhiên.

```java
Lock lock = new ReentrantLock();
try {
    lock.lock();
    System.out.println("locked");
}catch (Exception e){
    e.printStackTrace();
}finally {
    lock.unlock();
    System.out.println("Unlocked");
}
```

```java
package com.org.keywords.synchronization;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

public class LockDemo {
    private Integer number = 0;

    private ReentrantLock lock = new ReentrantLock();

    private Condition newCondition = lock.newCondition();

    public void incr() {
        try {
            lock.lock(); //Lock
            while (number != 0) {
                newCondition.await();//sleeping
            }
            number++;
            System.out.println(Thread.currentThread().getName() + "::" + number);
            newCondition.signal(); //Wake up another sleeping thread
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decr() {
        try {
            lock.lock();
            while (number != 1) {
                newCondition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName() + "::" + number);
            newCondition.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        LockDemo share = new LockDemo();

        new Thread(()->{
            for (int i=0;i<=10;i++){
                share.incr();
            }
        },"AA").start();

        new Thread(()->{
            for (int i=0;i<=10;i++){
                share.decr();
            }
        },"BB").start();
        /**
         * AA::1
         * BB::0
         * AA::1
         * BB::0
         * .....
         */
    }
}
```

> Trong Java, `ReentrantLock` là một cơ chế đồng bộ hóa được sử dụng để bảo vệ các phần của mã khỏi sự cạnh tranh và đồng bộ hóa luồng. Hai phương thức chính trong `ReentrantLock` là `lock()` và `unlock()`, chúng là phương thức quan trọng để kiểm soát việc truy cập vào các phần tài nguyên được bảo vệ.
>
> 1. **lock()**: Phương thức `lock()` được sử dụng để yêu cầu khóa tài nguyên. Khi một luồng gọi `lock()` trên một `ReentrantLock`, nó sẽ yêu cầu khóa và chờ đến khi nó nhận được khóa. Nếu khóa đang được sử dụng bởi một luồng khác, luồng hiện tại sẽ bị chặn (hoặc đợi) cho đến khi khóa trở thành khả dụng. Một lưu ý quan trọng là `ReentrantLock` cho phép một luồng có thể gọi `lock()` nhiều lần mà không gặp lỗi. Nếu luồng đã có khóa, mỗi lần gọi `lock()` sẽ tăng một số lượng đếm và phải có một lượng tương đồng của `unlock()` để giảm đếm và giải phóng khóa. Điều này làm cho `ReentrantLock` trở thành một loại khóa có thể đệ quy.
> 2. **unlock()**: Phương thức `unlock()` được sử dụng để giải phóng khóa tài nguyên. Khi một luồng gọi `unlock()` trên một `ReentrantLock`, nó sẽ giải phóng khóa và cho phép các luồng khác yêu cầu và sử dụng tài nguyên bảo vệ. Nếu đếm khóa đang là 0 sau khi gọi `unlock()`, tài nguyên sẽ được giải phóng và luồng khác có thể tiếp tục sử dụng nó. Tuy nhiên, nếu đếm khóa vẫn còn lớn hơn 0, khóa sẽ vẫn còn và chỉ giảm đếm. Chỉ khi đếm khóa đạt đến 0, tài nguyên mới được thực sự giải phóng.
>
> `ReentrantLock` cho phép luồng khóa và mở khóa một cách linh hoạt, cho phép kiểm soát chính xác hơn so với việc sử dụng `synchronized` truyền thống. Tuy nhiên, việc sử dụng `ReentrantLock` cần phải cẩn thận để tránh các vấn đề liên quan đến deadlock và khả năng luồng không bao giờ giải phóng khóa, gây ra vấn đề về hiệu suất.

> Trong Java, `java.util.concurrent.locks.Condition` là một thành phần quan trọng của cơ chế đồng bộ hóa được cung cấp bởi gói `java.util.concurrent.locks`. `Condition` được sử dụng để quản lý thứ tự thực thi của các luồng, đặc biệt là trong mô hình Producer-Consumer hoặc trong các tình huống đồng bộ hóa phức tạp hơn.
>
> Dưới đây là các phương thức quan trọng của `Condition`:
>
> 1. **await()**: Phương thức `await()` được sử dụng để đặt luồng hiện tại vào trạng thái chờ (waiting) cho đến khi một điều kiện nhất định được đáp ứng. Khi gọi `await()`, luồng sẽ giải phóng khóa mà nó đang giữ và chờ đến khi một luồng khác gọi `signal()` hoặc `signalAll()` trên cùng một `Condition`. Luồng sẽ tiếp tục thực thi sau khi nó được thức tỉnh (wake-up) và tái lấy khóa.
> 2. **awaitUninterruptibly()**: Tương tự như `await()`, nhưng không ảnh hưởng bởi các ngắt (interrupts). Khi gọi `awaitUninterruptibly()`, luồng sẽ chờ cho đến khi nó được thức tỉnh mà không quan tâm đến việc nó bị ngắt gián đoạn (interrupted).
> 3. **await(long time, TimeUnit unit)**: Giống như `await()`, nhưng luồng sẽ chờ cho một khoảng thời gian cụ thể trước khi tiếp tục thực thi, sau đó nó sẽ tự động tiếp tục thực thi nếu không có sự thức tỉnh nào xảy ra trong khoảng thời gian đã chỉ định.
> 4. **signal()**: Phương thức `signal()` được sử dụng để thức tỉnh một luồng đang chờ đợi trên `Condition`. Nó sẽ chọn một trong các luồng chờ (nếu có) và đánh thức nó để tiếp tục thực thi.
> 5. **signalAll()**: Phương thức `signalAll()` cũng thức tỉnh tất cả các luồng đang chờ trên `Condition`. Tất cả các luồng đang chờ sẽ được thức tỉnh và có cơ hội tiếp tục thực thi.
> 6. **awaitNanos(long nanosTimeout)**: Giống như `await(long time, TimeUnit unit)`, nhưng thời gian chờ là trong đơn vị nanos (nanoseconds).
>
> Các phương thức này cung cấp một cơ chế mạnh mẽ cho việc đồng bộ hóa luồng và quản lý thứ tự thực thi trong các ứng dụng đa luồng. Đặc biệt là trong các tình huống phức tạp như Producer-Consumer, sử dụng `Condition` có thể giúp tối ưu hóa hiệu suất và tránh tình trạng đơ (deadlock) hoặc tình trạng cạnh tranh (race condition).

ReentrantLock có nghĩa là khoá đăng nhập lại. Là Class duy nhất triển khai giao diện Lock, và ReentrantLock cung cấp nhiều phương thức hơn. là một Thread đã có được khoá và có thể lấy lại khoá mà không bị bế tắc.

## 7. Một giải thích khác về ReentrantLock

ReentrantLock được dịch là khoá Reentrant, có nghĩa là 1 Thread có thể liên tục khoá các tài nguyên được chia sẻ trong các phần quan trọng.

Cách phổ biến nhất để đảm bảo an toàn cho Thread là sử dụng các cơ chế (Lock, Synchronized) để thực hiện đồng bộ hoá đảm bảo an toàn cho tài nguyên được chia sẻ. Bằng cách này chỉ một Thread có thể thực thi một phương thức nhất định hoặc khối mã nhất định cùng một lúc và hoạt động phải đảm bảo tính nguyên tử, an toàn theo luồng.

Vì sao keyword `Synchronized` trong JDK cũng có thể hỗ trợ tính nguyên tử và an toàn luồng, thì tại sao chúng ta cần ReentrantLock sau khi đã có từ khoá `Synchronized`?

|                          | **Synchronized**                                             | **ReentrantLock**                                            |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Cơ chế                   | Synchronized sử dụng cơ chế Monitor Lock trong java, mà mỗi đối tượng trong java có một Monitor Lock đi kèm với nó | ReentrantLock sử dụng cơ chế AbstractQueuedSynchronizer (AQS) để triển khai khoá, cung cấp linh hoạt hơn trong việc quản lý khoá và điều khiển luồng. |
| Tính linh hoạt           | tính linh hoạt thấp, vì nó chỉ có thể đồng bộ hoá trên phương thức hoặc khối mã | cung cấp tính linh hoạt cao hơn với khả năng thực hiện các thao tác như đợi với thời gian chờ, hỗ trợ cho việc thức tính các luồng, và kiểm soát độ ưu tiên của các luồng. |
| Hình thức nhả khoá       | synchronized không hỗ trợ tính năng như phản ứng với ngắt, thời gian chờ, hoặc thử nghiệm để lấy khóa => nhả khoá tự động | ReentrantLock hỗ trợ các tính năng này như phản ứng với ngắt (interrupt), thời gian chờ (tryLock(long time, TimeUnit unit)), và thử nghiệm để lấy khóa (tryLock()). |
| Loại khoá được hỗ trợ    | synchronized chỉ hỗ trợ khóa không công bằng (non-fair lock). | ReentrantLock hỗ trợ cả loại khóa không công bằng và loại khóa công bằng (fair lock). |
| Hỗ trợ có điều kiện      | synchronized không có hỗ trợ cho việc tạo điều kiện và quản lý điều kiện. | ReentrantLock cung cấp phương thức newCondition() để tạo ra điều kiện và quản lý chúng. |
| Hỗ trợ khả năng tái nhập | `synchronized` và `ReentrantLock` đều hỗ trợ khả năng tái nhập, tức là một luồng có thể thực hiện lại việc giữ khóa mà nó đã giữ trước đó. | `synchronized` và `ReentrantLock` đều hỗ trợ khả năng tái nhập, tức là một luồng có thể thực hiện lại việc giữ khóa mà nó đã giữ trước đó. |

## 8. AQS là gì

AQS (AbstractQueuedSynchronizer) là một khung trừu tượng được sử dụng để xây dựng các đồng bộ hóa và khóa trong Java. Bằng cách kế thừa AQS, bạn có thể dễ dàng triển khai các đồng bộ hóa tùy chỉnh và các khóa để quản lý đa luồng. Dưới đây là một số điểm quan trọng về AQS:

1. **Mô hình**: AQS xây dựng trên một mô hình đồng bộ hóa hàng đợi trừu tượng. Nó sử dụng một danh sách hàng đợi (queue) của các luồng đang chờ để có thể thực hiện đồng bộ hóa một cách an toàn và hiệu quả.
2. **Cơ chế hoạt động**: AQS quản lý các luồng thông qua hai loại cơ chế: state (trạng thái) và queue (hàng đợi). Trạng thái đại diện cho trạng thái của tài nguyên được bảo vệ và có thể được thay đổi bởi các phương thức đồng bộ hóa. Hàng đợi chứa các luồng đang chờ để thực hiện các hành động liên quan đến tài nguyên.
3. **Triển khai**: Bạn có thể triển khai các lớp con của AQS để tạo ra các đồng bộ hóa tùy chỉnh hoặc các khóa tùy chỉnh. Bằng cách override các phương thức được cung cấp bởi AQS như `tryAcquire()`, `tryRelease()`, `tryAcquireShared()`, và `tryReleaseShared()`, bạn có thể điều khiển cách các luồng có thể truy cập vào tài nguyên.
4. **Hiệu suất**: AQS cung cấp một cơ chế đồng bộ hóa hiệu quả với overhead thấp, cho phép nhiều luồng đợi mà không gây ra quá nhiều tài nguyên hệ thống.
5. **Hỗ trợ cho Locks và Conditions**: AQS là cơ sở cho các khóa như ReentrantLock và các điều kiện như Condition trong Java. Các lớp này triển khai các phương thức cụ thể của AQS để cung cấp các tính năng như khả năng tái nhập, hỗ trợ cho điều kiện, và các tính năng khác.

Nhờ vào khả năng linh hoạt và hiệu suất của nó, AQS là một công cụ mạnh mẽ cho việc xây dựng các cơ chế đồng bộ hóa tùy chỉnh trong Java, cho phép bạn tạo ra các ứng dụng đa luồng an toàn và hiệu quả.

Như được hiển thị trong hình, các khóa và bộ đồng bộ hóa có liên quan trong gói java.util.concurrent (các gói thường được sử dụng bao gồm ReentrantLock, ReadWriteLock, CountDownLatch...) đều được triển khai dựa trên AQS

![](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Concurrency\imgs\615d8fa2b2104dfdab89c61892a40152~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp)

AQS là một mẫu thiết kế phương thức mẫu điển hình. Lớp cha (AQS) xác định khung và các chi tiết hoạt động nội bộ, đồng thời các quy tắc cụ thể được các lớp con triển khai.

---

*AQS AbstractQueuedSynchronizer là một khung trừ tượng, được sử dụng để xây dựng các đồng bộ hoá và khoá trong java.

1. Khởi tạo và cập nhật trạng thái

Khi một Luồng yêu cầu quyền truy cập vào 1 tài nguyên được đồng bộ hoá, AQS sẽ kiểm tra trạng thái của tài nguyên đó.

Nếu tài nguyên đang không được sử dụng (trạng thái là không khoá) Luồng hiện tại sẽ được gán làm Luồng chủ (owner Thread)  của tài nguyên và trạng thái của tài nguyên được cập nhật để đánh dấu là đã khoá.

trạng thái của tài nguyên được lưu trữ trong một biến thành viên `State` có kiểu dữ liệu là `int` được đánh dấu bằng từ khoá `volatile` để đảm bảo tính nhất quán và hiệu suất khi sử dụng trong môi trường đa luồng.

Sự thay đổi trạng thái của tài nguyên `State` được thực hiện thông qua các phép toán atomic như CAS (compare-and-swap) để đảm bảo tính toàn vẹn và sử lý đa luồng an toàn.

2. Cơ chế chờ và thức tỉnh (blocking and awaking merchanism)

nếu tài nguyên đã bị khoá bởi 1 luồng khác, luồng yêu cầu truy cập mới sẽ được đưa vào hàng đợi (waiting queue) của AQS.

AQS sử một biến thể của hàng đợi CLH (craig, landin, hagersten) để quản lý các luồng đang chờ quyền truy cập tài nguyên.

khi tài nguyên trở thành khả dụng (khoá đã được giải phóng), luồng đầu tiên trong hàng đợi queue của AQS sẽ được thức tỉnh (awaken) và cố gắng lấy khoá để thực hiện xử lý.

Tóm lại AQS hoạt động bằng cách sử dụng trạng thái của tài nguyên (State) và một cơ chế hàng đợi CLH để quản lý việc truy cập vào tài nguyên được đồng bộ hoá. Đảm bảo sự an toàn và tính nhất quán trong môi trường đa luồng. 

![](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Concurrency\imgs\1ebefeb23a184f2a9b515bf01eb430da~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp)

> Hình ảnh này minh họa cấu trúc và luồng của các luồng trong **AQS (AbstractQueuedSynchronizer)**. Dưới đây là mô tả chi tiết:
>
> 1. **AQS (AbstractQueuedSynchronizer)**:
>    - Là một cơ chế đồng bộ hóa trong Java, được sử dụng để quản lý trạng thái và luồng của các tài nguyên chia sẻ.
>    - Sử dụng hàng đợi CLH (Craig, Landin, and Hagersten) để duyệt qua các luồng đang chờ tài nguyên.
>    - Bao gồm các trạng thái như “không khóa”, “đang chờ”, “đã khóa” và “đã hủy”.
> 2. **CLH Queue (Craig, Landin, and Hagersten)**:
>    - Là một hàng đợi dựa trên danh sách liên kết đơn, được sử dụng để quản lý các luồng đang chờ tài nguyên.
>    - Các luồng được tự động thêm vào cuối hàng đợi khi chúng yêu cầu tài nguyên.
>    - Luồng chủ sở hữu độc quyền được xác định bởi trường “owner” trong nút CLH.
> 3. **Luồng (Thread) và Trạng thái**:
>    - Hình ảnh hiển thị các luồng (thread) được biểu diễn bằng các hình tròn.
>    - Mỗi luồng có trạng thái như “không khóa”, “đang chờ”, “đã khóa” hoặc “đã hủy”.
>    - Luồng chủ sở hữu độc quyền được chỉ định bằng mũi tên từ nút CLH đến luồng tương ứng.
>
> Hình ảnh này giúp hiểu rõ hơn về cách AQS và CLH Queue hoạt động trong việc quản lý tài nguyên đồng thời.

## 9. Các phương pháp và thuộc tính quan trọng hỗ trợ các tính năng AQS như sau:

```java
public abstract class AbstractQueuedSynchronizer 
  extends AbstractOwnableSynchronizer implements java.io.Serializable {
  	// CLH variant queue head and tail nodes
    private transient volatile Node head;
  	private transient volatile Node tail;
  	// AQS sync status
   	private volatile int state;
  	// Update state using CAS method
  	protected final boolean compareAndSetState(int expect, int update) {
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }
}
```

## 10. Queue CLH

Vì queue CLH được sử dụng trong AQS, trước tiên hãy hiểu hàng đợi CLH là gì? CLH là một loại hàng đợi queue, Craig, Landin, Hagersten là hàng đợi được thực hiện bởi một danh sách liên kết đơn trong môi trường đa luồng. Luồng ứng dụng chỉ quay trên các biến cục bộ, nó liên tục thăm dò trạng thái của node tiền nhiệm, nếu phát hiện nút tiền thân đã nhả khoá thì nó sẽ kết thúc vòng quay.

> CLH (Craig, Landin, and Hagersten) là một loại hàng đợi (queue) được thực hiện dưới dạng một danh sách liên kết đơn (single linked list) trong môi trường đa luồng. Được sử dụng chủ yếu trong các cơ chế đồng bộ hóa như AQS, CLH cung cấp một phương thức hiệu quả để quản lý việc truy cập vào tài nguyên được đồng bộ hóa. Dưới đây là một số chi tiết về cách CLH hoạt động:
>
> 1. **Kiến trúc hàng đợi**:
>    - Mỗi luồng yêu cầu truy cập vào tài nguyên được đồng bộ hóa sẽ được biểu diễn bằng một nút (node) trong hàng đợi CLH.
>    - Hàng đợi CLH được triển khai dưới dạng một danh sách liên kết đơn với mỗi nút chứa một tham chiếu đến nút tiền nhiệm (predecessor node).
> 2. **Hoạt động của luồng yêu cầu**:
>    - Khi một luồng yêu cầu truy cập vào tài nguyên, nó tạo một nút mới và thêm vào cuối hàng đợi CLH.
>    - Sau đó, luồng yêu cầu sẽ tự quay vòng (spin) trên trạng thái của nút tiền nhiệm của mình để kiểm tra xem liệu nút tiền nhiệm đã giải phóng tài nguyên chưa hay không.
>    - Luồng yêu cầu sẽ tiếp tục quay vòng cho đến khi nút tiền nhiệm của nó giải phóng tài nguyên, sau đó nó sẽ có thể tiếp tục và thực hiện truy cập vào tài nguyên.
> 3. **Thực hiện tự quay vòng (spinning)**:
>    - Tự quay vòng là quá trình luồng chờ đợi một sự kiện xảy ra mà không cần bị chặn hoặc bị ngủ (sleep).
>    - Trong CLH, luồng yêu cầu sẽ tự quay vòng bằng cách liên tục kiểm tra trạng thái của nút tiền nhiệm của mình.
>    - Nếu nút tiền nhiệm đã giải phóng tài nguyên, luồng yêu cầu có thể tiếp tục.
>
> Tóm lại, hàng đợi CLH cung cấp một cơ chế đơn giản và hiệu quả để quản lý truy cập vào tài nguyên được đồng bộ hóa trong môi trường đa luồng, nơi các luồng yêu cầu tự quay vòng trên trạng thái của nút tiền nhiệm để kiểm tra xem liệu tài nguyên đã sẵn sàng cho truy cập hay không.

![](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Concurrency\imgs\2b770f93d4a04da8904b7d3c13fcc25b~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp)

> Dựa trên những điểm mấu chốt về CLH Queue, chúng ta có thể kết luận những điều sau:
>
> 1. **CLH Queue là một hàng đợi (queue) đơn hướng**:
>    - CLH Queue duy trì tính chất FIFO (First In First Out) của một hàng đợi, điều này có nghĩa là các yêu cầu được thêm vào trước sẽ được xử lý trước.
> 2. **CLH Queue được xây dựng thông qua tail node**:
>    - Hàng đợi CLH được xây dựng thông qua tail node, một tham chiếu atomics luôn chỉ đến nút cuối cùng trong hàng đợi. Tham chiếu này giúp giảm thời gian tìm kiếm nút cuối cùng khi thêm mới một yêu cầu vào hàng đợi.
> 3. **Luồng chờ sẽ tự quay vòng thay vì chuyển đổi trạng thái**:
>    - Trong CLH Queue, các luồng yêu cầu truy cập vào tài nguyên sẽ tự quay vòng (spin) trên trạng thái của nút tiền nhiệm thay vì chuyển đổi trạng thái của chính nó. Điều này giúp tránh được chi phí chuyển đổi trạng thái và giảm thiểu overhead khi thực hiện đồng bộ hóa.
> 4. **Hiệu suất của CLH Queue không tốt khi có nhiều luồng chờ**:
>    - Trong môi trường có độ cao cạnh tranh cao, hiệu suất của CLH Queue có thể bị ảnh hưởng do các luồng chờ không ngừng tự quay vòng và kiểm tra trạng thái của nút tiền nhiệm. Điều này có thể dẫn đến overhead lớn và giảm hiệu suất tổng thể.
> 5. **AQS Queue là biến thể của CLH Queue**:
>    - Trong AQS (AbstractQueuedSynchronizer), hàng đợi được sử dụng để quản lý các yêu cầu truy cập vào tài nguyên được đồng bộ hóa là biến thể của CLH Queue. Mỗi luồng yêu cầu truy cập vào tài nguyên được biểu diễn bởi một nút trong hàng đợi, giúp quản lý việc phân phối khóa một cách hiệu quả.

> Từ mô tả của đoàn hệ CLH, có thể rút ra kết luận sau
>
> Hàng đợi CLH là danh sách liên kết một chiều duy trì các đặc điểm hàng đợi FIFO vào trước ra trước.
> Xây dựng hàng đợi thông qua nút đuôi (tham chiếu nguyên tử), luôn trỏ đến nút cuối cùng
> Các nút khóa không thu được sẽ quay thay vì chuyển trạng thái luồng.
> Hiệu suất kém khi tính đồng thời cao, vì nút chưa nhận được khóa liên tục thăm dò trạng thái của nút tiền nhiệm để xem liệu nó có nhận được khóa hay không.
>
> Hàng đợi trong AQS là hàng đợi hai chiều ảo của biến thể CLH. Việc phân bổ khóa đạt được bằng cách đóng gói từng luồng yêu cầu tài nguyên được chia sẻ vào một nút.

![](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Concurrency\imgs\0cf45df16b66457a93d1f69f96aeff38~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp)

> 
> So với hàng đợi CLH, hàng đợi chờ biến thể CLH trong AQS có các đặc điểm sau:
>
> 1. **Hàng đợi trong AQS là một danh sách liên kết hai chiều**:
>    - Trong AQS, hàng đợi chờ được triển khai dưới dạng một danh sách liên kết hai chiều, giúp việc thêm và loại bỏ các yêu cầu truy cập vào tài nguyên được đồng bộ hóa trở nên hiệu quả.
> 2. **Hàng đợi trong AQS duy trì tính chất FIFO**:
>    - Như CLH Queue, hàng đợi trong AQS vẫn duy trì tính chất FIFO (First In First Out), tức là các yêu cầu được thêm vào trước sẽ được xử lý trước.
> 3. **Sử dụng head và tail nodes**:
>    - Trong AQS, hàng đợi chờ được hình thành từ hai nút (head và tail) giúp quản lý cả hai đầu của hàng đợi. Tham chiếu đến head và tail được đánh dấu bằng từ khóa `volatile` để đảm bảo tính nhất quán và hiệu suất trong môi trường đa luồng.
> 4. **Thực hiện tự quay vòng và chuyển đổi sang chế độ chờ**:
>    - Khi một luồng không thể lấy được trạng thái đồng bộ hóa, nó sẽ tự quay vòng và thử lại một số lần. Nếu tự quay vòng không thành công, luồng sẽ chuyển sang chế độ chờ, giúp giảm bớt overhead so với việc tự quay vòng không ngừng như trong CLH Queue.
> 5. **Hiệu suất tốt hơn so với CLH Queue**:
>    - Do sử dụng chế độ tự quay vòng và chuyển đổi sang chế độ chờ khi cần thiết, hàng đợi chờ biến thể CLH trong AQS có hiệu suất tốt hơn trong môi trường đa luồng cao cạnh tranh. Điều này giúp giảm bớt overhead và tăng cường hiệu suất tổng thể của hệ thống.

## 11. AOS (Abstract Ownable Synchronizer)

AOS (AbstractOwnableSynchronizer) là một lớp trừu tượng trong Java, được sử dụng để theo dõi và quản lý luồng đang sở hữu một khóa đồng bộ hóa (mutex lock) trong các cơ chế đồng bộ hóa như AQS (AbstractQueuedSynchronizer). Dưới đây là một số điểm quan trọng về AOS:

1. **Thừa kế từ lớp trừu tượng**:
   - AOS là một lớp trừu tượng, có nghĩa là nó không thể được khởi tạo trực tiếp mà chỉ có thể được thừa kế bởi các lớp con.
2. **Chỉ chứa một biến Thread**:
   - Trong AOS, có một biến của lớp Thread, thường được gọi là "exclusiveOwnerThread" hoặc tương tự, dùng để lưu trữ thông tin về luồng đang sở hữu khóa.
3. **Phương thức để thiết lập và truy xuất luồng sở hữu**:
   - AOS cung cấp các phương thức để thiết lập và truy xuất luồng đang sở hữu khóa. Thông thường, có phương thức để đặt luồng sở hữu (owner thread) và một phương thức để trả về luồng sở hữu hiện tại.
4. **Quản lý luồng sở hữu của khóa đồng bộ**:
   - Chức năng chính của AOS là ghi nhận và theo dõi luồng đang sở hữu một khóa đồng bộ hóa. Điều này hữu ích trong việc quản lý việc phân phối và giải phóng khóa một cách hiệu quả.

Trong tổ chức của AQS, AOS thường được sử dụng để quản lý thông tin về luồng đang sở hữu khóa trong các cơ chế đồng bộ hóa như khóa đồng bộ hóa (mutex lock) trong Java.

> Lớp trừu tượng AQS cũng kế thừa từ lớp trừu tượng AOS (AbstractOwnableSynchronizer) [ AQS extends AOS ]
>
> Chỉ có một biến loại Thread bên trong AOS, cung cấp các phương thức để lấy và thiết lập luồng khóa độc quyền hiện tại.
>
> Chức năng chính là ghi nhận phiên bản luồng hiện đang chiếm một khóa độc quyền (khóa mutex)

```java
public abstract class AbstractOwnableSynchronizer implements java.io.Serializable {
    // Exclusive thread (does not participate in serialization)
    private transient Thread exclusiveOwnerThread;
    // Set the current exclusive thread
    protected final void setExclusiveOwnerThread(Thread thread) {
        exclusiveOwnerThread = thread;
    }
    // Returns the currently exclusive thread
    protected final Thread getExclusiveOwnerThread() {
        return exclusiveOwnerThread;
    }
}
```

Tại sao làm việc với AQS có thể phản ánh trình độ của coder, tức là làm chủ các công nghệ mà mọi người ít chú ý. Đây là lý do tại sao AQS thường xuất hiện trong các cuộc interview, bởi vì nó không hề đơn giản. Khi lần đầu tiếp xúc với ReentrantLock và AQS, tôi đã thấy mã nguồn hoàn toàn lộn xộn và việc debugging không hề dễ dàng, tôi cũng tin đây là phản ứng của hầu hết mọi người. 
