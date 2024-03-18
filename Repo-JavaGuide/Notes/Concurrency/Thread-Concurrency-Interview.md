

## 1. What is executor framework in java?

Executor và ExecutorService được sử dụng cho các mục đích sau

+ tạo Thread trong java
+ quản lý vòng đời của Thread trong java
+ kích hoạt các tác vụ trong Thread

Executor hỗ trợ tạo Thread Pool và quản lý vòng đời của tất cả các Thread trong Thread Pool đó.

Trong Executor framework, interface Executor và interface ExecutorSerivce được sử dụng nổi bật (sử dụng nhiều nhất trong java concurrent)

Interface Executor cung cấp phương thức execute(), rất quan trọng để thực thi tác vụ trong Thread.

Interface ExecutorService implements Executor, cung cấp các phương thức cho phép

- quản lý tác vụ và chấm dứt tác vụ (theo dõi tác vụ, chấm dứt tác vụ)
- các method có thể return về Future để theo dõi tiến trình tác vụ trong môi trường đa luồng.

Một ExecutorService cung cấp các phương thức để quản lý việc chấm dứt và các phương thức có thể taoh ra Future để theo dõi tiến trình của một hoặc nhiều tác vụ không đồng bộ.

---

## 2. What are differences between execute() and submit() method of executor framework in java?

---

## 3. **What is Semaphore in java?**

Semaphore kiểm soát quyền truy cập vào tài nguyên được chia sẽ trong môi trường đa luồng bằng cách sử dụng giấy phép trong java.

Nếu giấy phép > 0, thì semaphore cho phép truy cập vào tài nguyên được chia sẻ.

Nếu giấy phép = 0 or < 0, thì semaphore ko cho phép truy cập vào tài nguyên được chia sẻ trong java.

Những giấy phép này là một loại bộ đếm, cho phép truy cập vào tài nguyên được chia sẻ. Vì vậy, để truy cập tài nguyên, một Thread phải được cấp phép từ Semaphore trong java. 

Semaphore có 2 constructor

```java
public Semaphore(int permits);
public Semaphore(int permits, boolean fair)
```

Semaphore(int permits) là số lượng giấy phép ban đầu có sẳn. Giá trị này có thể âm, trong trường hợp đó việc phát hành phải xảy ra trước khi bất kỳ hoạt động mua lại nào được cấp, giấy phép là số lượng luồng có thể truy cập tài nguyên được chia sẻ tại một thời điểm. nếu giấy phép là 1 thì chỉ có một luongf có thể truy cập tài nguyên được chia sẻ tại một thời điêm trong java.

Semaphore(int permits, boolean fair) permits là số lượng giấy phép ban đầu có sẳn, giá trị này có thể âm, trong trường hợp đó việc phát hành xảy ra trước khi bất kỳ hoạt động mua lại nào được cấp. Bằng cách đặt fair thành true, chúng tôi đảm bảo rằng các chuỗi chờ được cấp giấy phép theo thứ chúng yêu cầu quyền truy cập. 

### 3.1 Semaphore là gì

Là Class cung cấp khả năng đồng bộ hoá luồng, được sử dụng chủ yếu cho nhiều luồng thực hiện các hoạt động song song trên các tài nguyên được chia sẻ. Có nghĩa là nó đại diện cho khái niệm cho phép nhiều luồng hoạt động trên cùng một tài nguyên. Sử dụng semaphore, bạn có thể kiểm soát số lượng thread đồng thờ truy cập vào tài nguyên.  

### 3.2 Hãy ví dụ một trường hợp

Kinh doanh bãi đậu xe: trước khi vào bãi xe, sẽ có thông báo bãi xe còn bao nhiêu chỗ có thể đậu, khi chỗ đậu xe về 0 thì bạn ko thể vào bãi đậu xe.

Các yếu tố chính: tổng số chỗ của bãi đậu xe, khi một xe vào bãi và đậu (số lượng chỗ đậu - 1), khi một xe rời đi (số lượng chỗ đậu + 1). Khi bãi xe đã đầy, không còn chỗ trống thì xe chỉ có thể đợi ở bên ngoài bãi.

> Trong Java, Semaphore là một lớp trong gói `java.util.concurrent` được sử dụng để quản lý việc truy cập vào các tài nguyên có hạn trong môi trường đa luồng. Dưới đây là các phương thức chính của Semaphore:
>
> 1. **`acquire()`**: Phương thức này được sử dụng để yêu cầu quyền truy cập tới tài nguyên. Nếu có cơ hội truy cập khả dụng (bộ đếm Semaphore không bằng 0), luồng sẽ tiếp tục thực hiện và giảm bộ đếm Semaphore. Nếu không có cơ hội truy cập khả dụng, luồng sẽ chờ đến khi có cơ hội truy cập.
> 2. **`acquire(int permits)`**: Tương tự như `acquire()` nhưng cho phép yêu cầu một số lượng permit cụ thể.
> 3. **`release()`**: Phương thức này được sử dụng để hoàn trả quyền truy cập tới tài nguyên. Nó tăng bộ đếm Semaphore và giải phóng một permit.
> 4. **`release(int permits)`**: Tương tự như `release()` nhưng cho phép giải phóng một số lượng permit cụ thể.
> 5. **`availablePermits()`**: Phương thức này trả về số lượng permit còn lại có sẵn để được cấp phát, mà không chờ đợi.
> 6. **`getQueueLength()`**: Phương thức này trả về số lượng luồng đang chờ để có thể thực hiện việc gọi phương thức `acquire()`. Điều này có thể được sử dụng để đo lường khả năng chịu tải của hệ thống.
> 7. **`tryAcquire()`**: Phương thức này cố gắng yêu cầu quyền truy cập tới tài nguyên mà không chờ đợi. Nếu có cơ hội truy cập khả dụng, nó sẽ trả về `true` và giảm bộ đếm Semaphore. Nếu không có cơ hội truy cập khả dụng, nó sẽ trả về `false`.
> 8. **`tryAcquire(int permits)`**: Tương tự như `tryAcquire()` nhưng cho phép yêu cầu một số lượng permit cụ thể.
>
> Đây là các phương thức cơ bản của Semaphore trong Java. Chúng cung cấp cơ chế linh hoạt để quản lý việc truy cập tới các tài nguyên có hạn trong môi trường đa luồng.

```java
public class CarParking {
    private static Semaphore semaphore = new Semaphore(2);
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(4);
        for (int i = 1; i <= 5; i++) {
            executor.submit(new MySemaphore(i, semaphore));
        }
    }
}

class MySemaphore implements Runnable {
    private int i;
    private Semaphore semaphore;

    public MySemaphore(int i, Semaphore semaphore) {
        this.i = i;
        this.semaphore = semaphore;
    }

    @Override
    public void run() {
        System.out.println("welcome " + Thread.currentThread().getName() + " Arrive at the parking lot");
        // Determine whether parking is allowed
        if(semaphore.availablePermits() == 0) {
            System.out.println("Insufficient parking spaces, please wait patiently");
        }
        try {
            // try to get
            semaphore.acquire();
            System.out.println(Thread.currentThread().getName() + " >> Enter << the parking lot");
            // Thread.sleep(new Random().nextInt(10000));// Simulate the time a vehicle spends in a parking lot
            Thread.sleep(5000);
            System.out.println(Thread.currentThread().getName() + " --- Exit --- the parking lot");
            semaphore.release();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

phương thức acquire() có 2 dạng 

```java
public void acquire() throws InterruptedException;
public void acquire(int permits) throws InterruptedException;
```

void acquire() có được giấy phép nếu có và giảm số lượng giấy phép có sẳn (-1), nếu ko có giấy phép thì Thread hiện tại sẽ ko hoạt động cho đến khi

- một Thread khác gọi method release() trên semaphore này
- một Thread khác làm gián đoạn Thread hiện tại

void acquire(int permits) đạt được số lượng giấy phép nếu có và giảm số lượng giấy phép có sẳn với (permits giấy phép), nếu số lượng giấy phép ko có sẳn thì Thread sẽ dừng chờ cho đến khi một trong những điều sau xảy ra.

- một số Thread khác gọi phương thức release() trên semaphore này và các giấy phép có sẳn lớn hơn hoặc bằng số lượng giấy phép cần.
- Một số Thread khác làm gián đoạn Thread hiện tại.

 ```java
 public void release();
 public void release(int permits);
 ```

Phát hành giấy phép và tăng số lượng giấy phép lên 1 (+1).

Phát hành giấy phép và tăng số lượng giấy phép lên permits lần (+permits).

## 4. How can you implement Producer Consumer pattern using Semaphore in java?

## 5. How can you implement your own Semaphore?
