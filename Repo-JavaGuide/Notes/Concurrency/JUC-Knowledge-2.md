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
