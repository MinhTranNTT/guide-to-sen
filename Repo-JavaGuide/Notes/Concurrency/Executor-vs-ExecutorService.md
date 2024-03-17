## **Executor vs ExecutorService**

---

Trong hướng dẫn về Thread Concurrency, chúng ta sẽ tìm hiểu về java.util.concurrent.Executor và java.util.concurrent.ExecutorService framework trong Java là gì thông qua một số ví dụ. Chúng ta sẽ tìm hiểu cách sử dụng Executor và ExecutorService framework trong việc xử lý Thread Concurrency trong Java. Và chúng ta cũng sẽ tìm hiểu các phương thức quan trọng trong Executor như execute() và ExecutorService như submit(), awaitTermination(), shutdown() trong Java.

Chúng ta cũng sẽ tìm hiểu về java.util.concurrent.Future và java.util.concurrent.Callable trong Java.   

---

### 1. What is java.util.concurrent.Executor in java?

Executor là một interface cốt lõi, ở mức trừu tượng. Nó cung cấp nền tảng cho Executor Framework thực thi các tác vụ không đồng bộ linh hoạt, mạnh mẽ mà không cần quan tâm đến chi tiết cụ thể quản lý luồng.

> Dưới đây là một số điểm quan trọng về `java.util.concurrent.Executor`:
>
> 1. **Thực thi Bất đồng bộ (Asynchronous Execution)**: Giao diện `Executor` cho phép thực hiện các nhiệm vụ bất đồng bộ mà không cần tạo ra trực tiếp các luồng mới. Thay vào đó, nó sử dụng một pool luồng có sẵn hoặc một cơ chế quản lý luồng để thực hiện các nhiệm vụ một cách hiệu quả.
> 2. **Tách Biệt Quy tắc và Cơ chế (Separation of Concerns)**: Giao diện này tách biệt quy tắc về cách thực thi các nhiệm vụ từ cơ chế quản lý luồng thực sự. Điều này cho phép bạn tập trung vào việc thực hiện nhiệm vụ mà không cần lo lắng về chi tiết cụ thể của việc quản lý luồng.
> 3. **Thiết kế Linh hoạt (Flexible Design)**: `Executor` cung cấp một loạt các cơ chế thực thi khác nhau, cho phép bạn lựa chọn cách thực thi nhiệm vụ phù hợp với nhu cầu cụ thể của ứng dụng.
> 4. **Tăng Hiệu suất (Improved Performance)**: Sử dụng `Executor` có thể cải thiện hiệu suất của ứng dụng bằng cách tái sử dụng các luồng đã có thay vì tạo ra luồng mới mỗi khi cần thực hiện một nhiệm vụ.
> 5. **Đa nhiệm (Concurrency)**: `Executor` cho phép thực thi nhiều nhiệm vụ cùng một lúc, giúp tăng hiệu suất và sự đáp ứng của ứng dụng trong môi trường đa nhiệm.

Vòng đời của Executor: việc triển khai Executor thường sử dụng một pool luồng có sẳn hoặc được tạo ra từ cơ chế quản lý luồng để thực hiện tác vụ, nhưng nếu Executor không thể tắt đúng cách thì JVM cũng không thể tắt được. 

Để giải quyết vấn đề này, Executor mở rộng interface ExecutorService (ExecutorService extends Executor) và được bổ sung một trên interface này một số phương thức quản lý vòng đời. Vòng đời Executor có 4 giai đoạn sau: đang chạy, đóng, và chấm dứt.

> `ExecutorService` là một giao diện trong Java được sử dụng để thực hiện các nhiệm vụ bất đồng bộ trên một pool luồng. Trạng thái vòng đời của `ExecutorService` bao gồm các giai đoạn sau:
>
> 1. **New (Mới)**: Trạng thái ban đầu của `ExecutorService` khi nó mới được tạo ra, trước khi bất kỳ nhiệm vụ nào được thêm vào hoặc thực thi.
> 2. **Running (Đang chạy)**: Trạng thái trong đó `ExecutorService` đang chạy và sẵn sàng thực thi các nhiệm vụ. Trong giai đoạn này, các nhiệm vụ được gửi đến `ExecutorService` sẽ được thực thi nếu có luồng trống trong pool hoặc nếu pool luồng không đạt tới giới hạn.
> 3. **Shutdown (Dừng)**: Trạng thái mà `ExecutorService` đã dừng nhận bất kỳ nhiệm vụ mới nào, nhưng nhiệm vụ hiện có trong hàng đợi vẫn được thực thi cho đến khi tất cả chúng hoàn thành.
> 4. **Terminated (Kết thúc)**: Trạng thái cuối cùng của `ExecutorService` sau khi tất cả các nhiệm vụ đã được thực thi xong và tất cả các luồng trong pool đã được giải phóng.
>
> Các phương thức quan trọng trong việc quản lý trạng thái của `ExecutorService` bao gồm:
>
> - `shutdown()`: Chuyển `ExecutorService` sang trạng thái "shutdown", ngăn chặn việc nhận bất kỳ nhiệm vụ mới nào, nhưng vẫn cho phép các nhiệm vụ hiện có trong hàng đợi được thực thi.
> - `shutdownNow()`: Chuyển `ExecutorService` sang trạng thái "shutdown" và cố gắng ngừng thực thi tất cả các nhiệm vụ hiện đang chờ trong hàng đợi.
> - `isShutdown()`: Kiểm tra xem `ExecutorService` đã ở trong trạng thái "shutdown" chưa.
> - `isTerminated()`: Kiểm tra xem `ExecutorService` đã ở trong trạng thái "terminated" chưa, tức là không còn nhiệm vụ nào đang thực thi và tất cả các luồng trong pool đã được giải phóng.
> - `awaitTermination(long timeout, TimeUnit unit)`: Chờ đợi cho đến khi `ExecutorService` chuyển sang trạng thái "terminated" hoặc cho đến khi hết thời gian chờ.

*giải thích 2:*

Interface java.util.concurrent.Executor cung cấp phương thức rất quan trọng execute(), để triển khai thực thi lệnh trong java.

 ```java
 void execute(Runnable command)
 ```

Thực hiện các lệnh được triển khai, tuỳ thuộc loại triển khai.

- một Thread
- một pool Thread

### 2. java.util.concurrent.ExecutorService in java?

Interface ExecutorSerive extends interface Executor, cung cấp các phương thức với các mục đích sau

- quản lý thread hoặc pool thread
- các phương thức có thể tạo ra Future để theo dõi tiến độ của nhiệm vụ trong quá trình thực thi

#### 2.1 Các phương thức của ExecutorService

```java
boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException
```

block cho đến khi một trong những điều sau xảy ra

- Thread hiện tại bị gián đoạn (interrupted)
- hết thời gian chờ chỉ định
- đợi cho đến khi tất cả các nhiệm vụ được thực thi hoàn thành và ExecutorService chuyển trạng thái sang `terminated` 

> **Trả về:**
>
> - `true` nếu `ExecutorService` đã chuyển sang trạng thái "terminated" trước khi hết thời gian chờ.
> - `false` nếu đã hết thời gian chờ mà `ExecutorService` vẫn chưa chuyển sang trạng thái "terminated".
>
> **Lưu ý:**
>
> - Phương thức này thường được sử dụng sau khi gọi phương thức `shutdown()` hoặc `shutdownNow()` để chờ đợi cho đến khi tất cả các nhiệm vụ đã được thực thi xong và `ExecutorService` kết thúc hoạt động.
> - Việc sử dụng phương thức này trong một luồng sẽ chặn luồng đó cho đến khi điều kiện chờ đợi được đáp ứng, do đó cần phải cẩn thận khi sử dụng trong môi trường đa luồng để tránh làm chậm toàn bộ ứng dụng.

```java
<T> Future<T> submit(Callable<T> task)
```

Gửi lên một nhiệm vụ để thực hiện, phương thức trả về Future là đại diện cho kết quả đang chờ xử lý của nhiệm vụ.

Sau khi hoàn thành nhiệm vụ, phương thức get() của Future sẽ trả về kết quả của nhiệm vụ.

```java
<T> Future<T> submit(Runnable task, T result)
```

Gửi lên một nhiệm vụ Runnable để thực hiện, phương thức trả về Future là đại diện cho kết quả đang chờ xử lý của nhiệm vụ.

Sau khi hoàn thành nhiệm vụ, phương thức get() của Future sẽ trả về kết quả của nhiệm vụ.

```java
Future<?> submit(Runnable task)
```

Gửi lên một nhiệm vụ Runnable để thực hiện, phương thức trả về Future là đại diện cho kết quả đang chờ xử lý của nhiệm vụ.

Sau khi hoàn thành nhiệm vụ, phương thức get() của Future sẽ trả về **null** trong java.

```java
void shutdown()
```

Thực hiện tắt trình thực thi, không nhận thêm tác vụ mới, các tác vụ đã được gửi trước đó sẽ được thực hiện cho đến khi hoàn thành tất cả.

```java
List<Runnable> shutdownNow()
```

- Thread hoặc pool Thread sẽ được tắt ngay lặp tức
- tất cả các nhiệm vụ đang thực thi đều bị dừng lại
- nhiệm vụ đang chờ sẽ không được thực thi
- phương thức trả về một danh sách các tác vụ đang chờ thực thi trong java

```java
boolean isTerminated()
```

Trả về True nếu tất cả các tác vụ đã hoàn thành sau khi tắt trong java.

```java
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException
```

> Phương thức `invokeAll()` của `ExecutorService` trong Java được sử dụng để thực thi một tập hợp các `Callable` và chờ đợi cho đến khi tất cả các `Callable` đã được thực thi và trả về kết quả.
>
> **Cách hoạt động:**
>
> - Phương thức `invokeAll()` nhận vào một danh sách các `Callable` và trả về một danh sách các đối tượng `Future`. Mỗi `Future` đại diện cho kết quả của một `Callable` tương ứng.
> - Khi bạn gọi `invokeAll()`, `ExecutorService` sẽ bắt đầu thực thi tất cả các `Callable` trong danh sách.
> - Phương thức này sẽ chờ đợi cho đến khi tất cả các `Callable` đã được thực thi xong hoặc cho đến khi hết thời gian chờ được chỉ định (nếu có).
> - Khi tất cả các `Callable` đã hoàn thành, danh sách các `Future` chứa kết quả của từng `Callable` sẽ được trả về.
>
> **Tham số:**
>
> - `callables`: Danh sách các `Callable` mà bạn muốn thực thi.
> - `timeout`: Thời gian tối đa để chờ đợi kết quả của tất cả các `Callable`. Nếu `null`, phương thức sẽ chờ đợi vô hạn.
>
> **Trả về:**
>
> - Một danh sách các đối tượng `Future`, mỗi đối tượng `Future` đại diện cho kết quả của một `Callable`.
>
> **Ví dụ:**
>
> ```
> javaCopy codeList<Callable<String>> callables = Arrays.asList(
>     () -> "Task 1",
>     () -> "Task 2",
>     () -> "Task 3");
> 
> ExecutorService executorService = Executors.newFixedThreadPool(3);
> List<Future<String>> futures = executorService.invokeAll(callables);
> 
> for (Future<String> future : futures) {
>     try {
>         System.out.println(future.get()); // Lấy kết quả từ mỗi Future
>     } catch (InterruptedException | ExecutionException e) {
>         e.printStackTrace();
>     }
> }
> 
> executorService.shutdown();
> ```
>
> Trong ví dụ này, `invokeAll()` sẽ thực thi tất cả các `Callable` trong danh sách `callables` và chờ đợi cho đến khi tất cả các `Callable` hoàn thành. Sau đó, chúng ta lặp qua danh sách `Future` và lấy kết quả của mỗi `Callable` bằng cách sử dụng phương thức `get()` của `Future`.

### 3.  Difference between Fixed and Cached Thread pool in Java Executor Famework?

Có 2 loại nhóm luồng được cung cấp bởi Executor Framework: 

- một là nhóm luồng cố định (fixed), bắt đầu với số lượng Thread cố định.
  - Executors.newFixedThreadPool được sử dụng để khởi tạo một nhóm Thread có kích thước cố định. Nếu số lượng tác vụ vượt hơn số lượng Thread được khởi tạo trong pool, thì các tác vụ còn lại sẽ được đưa vào hàng đợi và sẽ được thực thi tiếp tục ngay khi tác vụ bất kỳ hoàn thành.
- loại còn lại là nhóm luồng (Thread) được lưu trữ trong bộ nhớ đệm, để tạo ra thêm nhiều Thread hơn nếu số lượng Thread ban đầu đáp ứng thực thi. 
  - Executors.newCachedThreadPool được sử dụng để tạo nhóm Thread được lưu trữ trong bộ nhớ đệm. Nhóm Thread sẽ thực thi ngay khi nhận được tác vụ cần xử lý, nếu nhóm Thread không đủ  để đáp ứng tác vụ thì các Thread mới sẽ được tạo ra để đáp ứng thực thi tác vụ. 

> 1. Khi tác vụ được gửi đi, sẽ được thực thi ngay lập tức trong nhóm Thread được lưu trữ trong bộ nhớ đệm. Nhưng tác vụ đó có thể được đặt trong hàng đợi nếu không có Thread nào trống trong trường hợp khởi tạo với nhóm Thread cố định. Điều này có nghĩa, nếu bạn muốn đảm bảo thực thi ngay lập tức thì sử dụng khởi tạo nhóm Thread được lưu trữ trong bộ nhớ cache là lựa chọn tốt hơn, nhưng nếu bạn bị hạn chế về tài nguyên và muốn sử dụng số lượng Thread cố định thì khởi tạo với nhóm Thread cố định là lựa chọn tốt hơn.
> 2. Syntax Executors.newFixedThreadPool vs Executors.newCachedThreadPool, 2 cách khai báo bên trên là các phương thức khởi tạo một nhóm Thread. Là một phần của Java Concurrency API.
> 3. nhóm Thread được lưu trong bộ nhớ đệm có thể tạo thêm Thread nếu cần nhưng không có Thread bổ sung nào có thể được khởi tạo thêm trong nhóm Thread cố định. Điều này có ý nghĩa là nhóm Thread được lưu trữ trong bộ nhớ cache là lựa chọn tốt hơn nếu bạn có tài nguyên vì nó có thể tạo và tăng số lượng Thread để thực thi tác vụ, nhưng nhóm Thread cố định sẽ là lựa chọn tốt hơn nếu môi trường hạn chế về tài nguyên. 

![](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Concurrency\imgs\Fixed-Thread-Pool.png)

![](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Concurrency\imgs\Cached-Thread-Pool.png)

<<<<<<< HEAD
---

```java
public class MyThread implements Runnable {
    private int taskNumber;
    public MyThread(int taskNumber) {
        this.taskNumber = taskNumber;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()
                + " executing task no " + taskNumber);
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

```java
public class ExecutorBasic {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(2);
        for(int i=1; i<11; i++) {
            MyThread myThread = new MyThread(i);
            executor.execute(myThread);
        }
        executor.shutdown();
        System.out.println("shutdownThread");
    }
}
```

Trong ví dụ này số lượng task cần xử lý là 10, và executor hỗ trợ tạo 2 thread, có vai trò hỗ trợ xử lý 10 task này. khi shutdown() được gọi những task được gửi đi trước đó sẽ được hoàn thành và không nhận thêm task mới.

---

### 4. java.util.concurrent.Future<V> in java

Future là một giao diện cung cấp các phương thức với mục đích sau trong java

- nhận kết quả của quá trình xử lý, đợi cho đến khi tất cả các các vụ được gửi hoàn tất
- huỷ bỏ tính toán của một nhiệm vụ.

> *Future Methods in java >*
>
> *V* **get***() throws InterruptedException, ExecutionException;*
>
> Method returns the result of computation, method waits for computation to complete.
>
> *V* **get***(long* **timeout***, TimeUnit* **unit***) throws InterruptedException, ExecutionException, TimeoutException;*
>
> Method waits for at most **timeout** time for computation to complete, and then returns the result, if available.
>
> *cancel method* : method cancels the task.

---

### 5. java.util.concurrent.Callable<V> in java

=======
.
>>>>>>> 82458cd97c6c8a06aed595b617fa4553ba25616f




---

### 6. Runnable - Callable - Future

Khi khởi tạo một Class extends Thread, (bản thân Thread đã implements Runnable) thì Runnable là một giao diện chỉ có duy nhất method run() (giao diện chức năng: functional interface)

- không có tham số truyền vào (ko parameter)
- không có giá trị trả về (void)
- không throw ra bất kỳ exception hỗ trợ xử lý

```java
for(int i=0; i<11; i++) {
    Thread thread = new Thread(new MyThread(i));
    thread.start();
}
```

*Lưu ý rằng phương thức start() của Thread mới tạo sẽ được gọi thay vì gọi bằng run(). Mặc dù chúng ta ghi đè phương run() khi implements Runnable nhưng phương thức run() không có tác dụng việc tạo Thread mà nó sẽ thực thi trực tiếp trên Thread hiện tại, giống như việc thực thi một phương thức thông thường.

*Vậy tại sao start() được gọi? Hãy xem bên trong phương thức start() bên trong class Thread, nó gọi một phương thức native có tên start0(), phương thức này không được triển khai ở java mà ở cấp độ JVM, logic chính của phương thức là khởi động một luồng hệ điều hành và liên kết nó với luồng JVM. 

Tiếp nối sự phát triển Callable (@FunctionalInterface) được phát triển và được thêm kể từ jdk 1.5, giải quyết những thíu sót của giao diện Runnable mặc dù các kịch bản đều có thể triển khai bằng Runnable.

- cho phép trả về giá trị (return V)
- ném ra Exception handle các ngoại lệ

```java
for (int i = 0; i < 10; i++) {
    MyCallable myCallable = new MyCallable();
    FutureTask<String> stringFutureTask = new FutureTask<>(myCallable);
    Thread thread = new Thread(stringFutureTask);
    thread.start();
    String s = stringFutureTask.get();
    System.out.println(s);
}

public class MyCallable implements Callable<String> {
    public MyCallable() {
    }

    @Override
    public String call() throws Exception {
        return Thread.currentThread().getName();
    }
}
```

*Nó có vẻ hơi phức tạp, bạn cần sử dụng FutureTask, vì lớp Thread không hỗ trợ Constructor với Callable/ Phương thức get() từ FutureTask để nhận kết quả trả về trong quá trình thực thi. Trong quá trình phát triển đây là một bad practice, thay vào đó nên sử dụng nhóm luồng.

```java
ExecutorService executor = Executors.newFixedThreadPool(10);
for (int i = 0; i < 10; i++) {
    MyCallable myCallable = new MyCallable();
    Future<String> submit = executor.submit(myCallable);
    String s = submit.get();
    System.out.println(s);
}
```

> Tổng hợp: Trong java các Thread có thể được khởi tạo bằng Runnable, Callable và Future. Interface Callable tương tự như interface Runnable, nhưng Callable nó có thể trả về một kết quả và có thể đưa ra các ngoại lệ. Future được sử dụng để nhận kết quả từ một tiến trình không đồng bộ và đợi cho tất cả các luồng hoàn thành.
>
> Việc sử dụng Callable và Future để tạo thread có một số ưu điểm và lợi ích so với việc sử dụng trực tiếp giao diện Runnable hoặc lớp Thread:
>
> Khả năng trả về kết quả: Giao diện Callable có thể trả về kết quả được tính toán, rất hữu ích trong một số trường hợp nhất định. Ví dụ: nếu bạn cần thực hiện một tác vụ tính toán tốn nhiều thời gian và cần lấy kết quả tính toán để xử lý tiếp theo thì Callable có thể dễ dàng đáp ứng yêu cầu này.
> Khả năng ném ngoại lệ: Phương thức call() của giao diện Callable có thể được khai báo để ném ngoại lệ, cho phép xử lý tình huống lỗi tốt hơn khi ngoại lệ xảy ra trong quá trình thực thi, thay vì trực tiếp làm hỏng luồng.
> Quản lý nhóm luồng linh hoạt hơn: Giao diện ExecutorService cung cấp quản lý nhóm luồng linh hoạt, có thể dễ dàng kiểm soát số lượng luồng đồng thời, gửi tác vụ, nhận kết quả thực hiện tác vụ, v.v. Sử dụng Callable và Future kết hợp với ExecutorService có thể quản lý các tác vụ đồng thời thuận tiện hơn.
> Chặn và chờ hoàn thành tác vụ: Phương thức get() của đối tượng Future có thể chặn luồng hiện tại cho đến khi quá trình thực thi tác vụ hoàn thành và kết quả được trả về. Điều này rất hữu ích khi bạn cần đợi một tác vụ hoàn thành trước khi tiếp tục.
> Hỗ trợ hủy tác vụ: Đối tượng Future cung cấp phương thức cancel() để hủy việc thực thi tác vụ. Điều này có thể giúp tránh lãng phí tài nguyên hoặc xử lý các tác vụ hết thời gian chờ trong một số trường hợp.
> Nói chung, việc sử dụng Callable và Future để tạo luồng có thể cung cấp phương thức thực thi đồng thời linh hoạt và dễ kiểm soát hơn, giúp việc lập trình đa luồng trở nên thuận tiện, hiệu quả và an toàn hơn.

```java
what type of results Callable’s call() method can return in java?
The Callable<V> is a generic interface, so its call method can return generic result spcified by V.

V call() throws Exception;
method for computing a result.
Method returns computed result or throws an exception if unable to do so.

How Callable and Future are related in java?
If you submit a Callable object to an Executor returned object is of Future type.

Future<Double> futureDouble=executor.submit(new SquareDoubleCallable(2.2));

This Future object can check the status of a Callable call’s method and wait until Callable’s call() method is not completed.

SquareDoubleCallable is a class which implements Callable.
```

---

### 7. Sự khác nhau  giữa java.lang.Runnable and java.concurrent.Callable ?

điểm tương đồng: các Instance triển khai (implements) Callable và Runnable đều có thể thực thi bởi một Thread khác.

sự khác biệt: Class implements Callable phải ghi đè method call(), method call() sẽ trả về một giá trị và ném ra một Exception nếu có. Class Implements Runnable phải ghi đè phương thức run(), method run() này không trả về giá trị và không thể đưa ra ngoại lệ nếu trong quá trình xử lý xảy ra các ngoại lệ, phải tự mình xử lý.

---

### 8. Using <T> Future<T> submit(Runnable task, T result) and Future<?> submit(Runnable task) in program in java >

```java
<T> Future<T> submit(Runnable task, T result)
```

Phương thức sẽ gửi đi Runnable cho Thread thực hiện xử lý, Method sẽ trả về một Future đại diện cho task đó, Khi task hoàn thành xử lý từ Future có thể sử dụng method get() để nhận về kết quả của task.

  ```java
  Future<?> submit(Runnable task)
  ```

Phương thức sẽ gửi đi Runnable cho Thread thực hiện xử lý, Method sẽ trả về một Future đại diện cho task đó. Khi nhiệm vụ hoàn thành, phương thức get của Future sẽ trả về null.

```java
class MyRunnable implements Runnable {
    @Override
    public void run() {
           System.out.println("MyRunnable's run()");     
    }
}
 
public class SubmitRunnableExample {
  private static final int NTHREDS = 10;
 
  public static void main(String[] args) throws InterruptedException, ExecutionException {
    ExecutorService executor = Executors.newFixedThreadPool(NTHREDS);

    Future<Integer> futureInteger=executor.submit(new MyRunnable(), 1);
    System.out.println("futureInteger.get() > "+futureInteger.get());
     
    Future<?> future=executor.submit(new MyRunnable());
    System.out.println("future.get() > "+future.get());
  }
}
```

---

### 9. Differences between execute() and submit() method of executor framework in thread concurrency in java >

| **execute()** method                                       | **submit()** method                                          |
| :--------------------------------------------------------- | ------------------------------------------------------------ |
| execute() được định nghĩa trong interface Executor         | submit() được định nghĩa trong interface ExecutorService     |
| Nó được sử dụng để thực thi các tác vụ implements Runnable | Nó được sử dụng để thực thi các tác vụ implements Runnable/ Callable.<br />Submit() sẽ trả về một Future đại diện cho task trong quá trình xử lý. Từ Future có thể quản lý task đó theo nhu cầu. |
| - void execute(Runnable command);                          | - <T> Future<T> submit(Callable<T> task);<br />Phương thức trả về Future đại diện cho tác vụ đang chờ xử lý, Khi tác vụ hoàn thành phương thức get() của Future sẽ trả về kết quả các tác vụ đó <br />- <T> Future<T> submit(Runnable task, T result);<br />Phương thức trả về Future đại diện cho tác vụ đang chờ xử lý, khi tác vụ hoàn thành phương thức get() của Future sẽ trả về result T<br />- Future<?> submit(Runnable task);<br />Trả về một Future là đại diện cho tác vụ đang chờ xử lý, khi tác vụ thực thi thành công, method get() sẽ trả về null. |

















## References:

[blog/java并发.md at 2dec5a881bdc44c19b5a5a19d9e02de49349d94f · dackh/blog (github.com)](https://github.com/dackh/blog/blob/2dec5a881bdc44c19b5a5a19d9e02de49349d94f/java并发.md)

[JavaMadeSoEasy.com (JMSE): Differences between execute() and submit() method of executor framework in java](https://www.javamadesoeasy.com/2015/03/differences-between-execute-and-submit.html)

[JavaMadeSoEasy.com (JMSE): Executor and ExecutorService framework in java - Detailed explanation with full program](https://www.javamadesoeasy.com/2015/03/executor-and-executorservice-framework.html)

[Java ExecutorService (jenkov.com)](https://jenkov.com/tutorials/java-util-concurrent/executorservice.html)

[Java Executor interface (java2s.com)](http://www.java2s.com/ref/java/java-executor-interface.html)

[A Guide to the Java ExecutorService | Baeldung](https://www.baeldung.com/java-executor-service-tutorial)

[Executor vs ExecutorService in Java (javaguides.net)](https://www.javaguides.net/2023/11/executor-vs-executorservice-in-java.html#:~:text=Key Points-,1.,one or more asynchronous tasks.)

[java - what's the difference between Executor and ExecutorService? - Stack Overflow](https://stackoverflow.com/questions/15052317/whats-the-difference-between-executor-and-executorservice)

[Difference between Executor, ExecutorService and Executers class in Java (javarevisited.blogspot.com)](https://javarevisited.blogspot.com/2017/02/difference-between-executor-executorservice-and-executors-in-java.html)

[Executor 与 ExecutorService 和 Executors 傻傻分不清_executorservice executors-CSDN博客](https://blog.csdn.net/moakun/article/details/88627660)

[Difference between Fixed and Cached Thread pool in Java Executor Famework | Java67](https://www.java67.com/2022/05/difference-between-fixed-and-cached-thread-pool.html)











