## **Executor vs ExecutorService**

---

Trong hướng dẫn về Thread Concurrency, chúng ta sẽ tìm hiểu về java.util.concurrent.Executor và java.util.concurrent.ExecutorService framework trong Java là gì thông qua một số ví dụ. Chúng ta sẽ tìm hiểu cách sử dụng Executor và ExecutorService framework trong việc xử lý Thread Concurrency trong Java. Và chúng ta cũng sẽ tìm hiểu các phương thức quan trọng trong Executor như execute() và ExecutorService như submit(), awaitTermination(), shutdown() trong Java.

Chúng ta cũng sẽ tìm hiểu về java.util.concurrent.Future và java.util.concurrent.Callable trong Java.   

---

1) What is java.util.concurrent.Executor in java?

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

2. java.util.concurrent.ExecutorService in java?

Interface ExecutorSerive extends interface Executor, cung cấp các phương thức với các mục đích sau

- quản lý thread hoặc pool thread
- các phương thức có thể tạo ra Future để theo dõi tiến độ của nhiệm vụ trong quá trình thực thi

2.1 Các phương thức của ExecutorService

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













