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

