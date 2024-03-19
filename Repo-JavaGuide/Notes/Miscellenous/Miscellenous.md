## 1. Giải thích tại sao yyyy lại khác YYYY trong SimpleDateFormat

```java
public static void main(String[] args) {
    Calendar calendar = Calendar.getInstance();
    calendar.set(2019, Calendar.DECEMBER, 31);
    Date strDate = calendar.getTime();
    DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
    System.out.println("After format [yyyy-MM-dd] on December 31, 2019：" + dateFormat.format(strDate));
    dateFormat = new SimpleDateFormat("YYYY-MM-dd");
    System.out.println("After format [yyyy-MM-dd] on December 31, 2019：" + dateFormat.format(strDate));
}
```

Sự khác biệt giữa hai kết quả là do việc sử dụng các ký tự "yyyy" và "YYYY" trong định dạng ngày tháng.

1. **yyyy**:
   - Trong `SimpleDateFormat`, "yyyy" biểu diễn cho năm dương lịch, nơi các giá trị từ 1 đến 9999 được chấp nhận.
   - Trong ví dụ của bạn, khi bạn sử dụng định dạng "yyyy-MM-dd", kết quả sẽ là "2019-12-31". Điều này đúng với ngày 31 tháng 12 năm 2019 theo lịch dương.
2. **YYYY**:
   - "YYYY" biểu diễn cho "week-based-year" trong lịch, nơi mà một năm được xác định bằng cách xem xét tuần của nó. Điều này thường được sử dụng trong các ứng dụng liên quan đến việc phân tích và hiển thị dữ liệu theo tuần.
   - Trong ví dụ của bạn, khi bạn sử dụng định dạng "YYYY-MM-dd", kết quả sẽ là "2020-12-31". Điều này là do tuần đầu tiên của năm 2020 bắt đầu từ ngày 30/12/2019 (Chủ nhật) và kết thúc vào ngày 05/01/2020. Do đó, năm "week-based-year" cho ngày 31/12/2019 sẽ là năm 2020.

Vậy nên, sự khác biệt giữa hai kết quả là do sự khác biệt trong ý nghĩa của các ký tự "yyyy" và "YYYY" trong định dạng ngày tháng.

## 2. Thread Pool trong java

### 2.1 Fundamentals

Khi xử lý các vấn đề với Concurrency, nếu chúng ta tạo các Thread mới mỗi lần thì áp lực của hệ thống là bao nhiêu? Lúc này, thread pool xuất hiện như một anh hùng, giúp chúng ta quản lý các Thread một cách hiệu quả, cải thiện việc sử dụng tài nguyên và giảm chi phí. 

- Trước hết, Thread Pool có thể kiểm soát số lượng luồng trong hệ thống, giúp giảm chi phí tạo và hủy luồng, đồng thời cải thiện khả năng phản hồi của hệ thống. 
- Thứ hai, với cấu hình phù hợp, nhóm luồng có thể mang lại sự ổn định cho hệ thống tốt hơn và tránh sự cố hệ thống do có quá nhiều chủ đề.

Chúng ta sẽ đi sâu vào Thread Pool trong Java để xem nó hoạt động như thế nào, cách sử dụng và cách tận dụng tối đa mã của chúng ta.

Thread Pool là nơi lưu trữ các Thread. Và quan trọng hơn là nó quản lý các Thread một các hiệu quả, bao gồm các yêu cầu tạo mới Thread, huỷ Thread, ... Trong java Thread pool được cung cấp thông qua giao diện Executor và các lớp triển khai giao diện ExecutorService, ThreadPoolExecutor (ThreadPoolExecutor extends AbstractExecutorService) 

- [AbstractExecutorService implements ExecutorService]  
- [ExecutorService extends Executor]  

Cuối cùng, ý tưởng cốt lõi của Thread pool là tái sử dụng lại các Thread hiện có. Khi một tác vụ xuất hiện, Thread Pool sẽ cố gắng sử dụng một Thread hiện có thay vì tạo Thread mới mỗi lần. Nếu tất cả các Thread trong Pool đều đang bận, Thread Pool sẽ tạo mới nhóm Thread và gửi nó vào hàng đợi. Đều này làm giảm đáng kể chi phí tạo và huỷ Thread.

![](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Miscellenous\imgs\Thread-Pool-Sequence.PNG)

> The image you sent depicts the Java Threadpool mechanism. Here’s a breakdown of the sequence of events:
>
> 1. **Submit task:** The application submits a task to the ThreadPool.
> 2. **Add task to queue:** The ThreadPool adds the task to the TaskQueue.
> 3. **Task available:** The TaskQueue manages the tasks and checks if there's an available task.
> 4. **Assign task to thread:** If a task is available, the TaskQueue assigns the task to a thread.
> 5. **Execute task:** The thread executes the task.
> 6. **Task completed:** Once the task is completed, the thread is freed up to handle new tasks.
> 7. **Queue empty:** If there are no more tasks in the queue, the thread goes into a waiting state.
>
> In essence, the ThreadPool acts as a  buffer that holds tasks waiting to be executed. The TaskQueue manages the tasks and allocates them to threads from the pool. This mechanism helps to optimize thread usage and prevent applications from creating too many threads, which can lead to performance issues.

### 2.2 Types of Thread Pool in java

> ExecutorService executor = Executors.newFixedThreadPool(int permits)

Số lượng Thread trong Thread Pool này là cố định, nó phù hợp với các tình huống xử lý ổn định, số Thread trong Thread Pool không đổi không có nghĩa là các Thread không được tạo và huỷ thường xuyên.

> ExecutorService cachedThreadPool = Executors.newCachedThreadPool();

Thread Pool này có thể tạo các Thread mới nếu cần nhưng ưu tiên sử dụng lại các Thread nếu chúng sẳn có. Đó là ý tưởng cho các tình huống trong đó số lượng nhiệm vụ thay đổi linh hoạt.

> ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();

Được sử dụng trong các tình huống cần thực hiện tuần tự các nhiệm vụ. Chỉ có 1 Thread hoạt động trong Thread Pool này, nó đảm bảo rằng tất cả các nhiệm vụ được thực thi theo thứ tự trong cùng một Thread.

> ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(5);

Cuối cùng, Thread Pool này đặc biệt phù hợp với các tình huống yêu cầu thực hiện các tác vụ theo thời gian hoặc định kỳ. Bạn có thể đặt tác vụ sẽ được thực thi sau một khoảng thời gian trễ được chỉ định hoặc theo các khoảng thời gian đều đặn.

Sumary:: 

Như chúng ta có thể thấy, Java cung cấp cho chúng ta nhiều loại thread pool khác nhau, mỗi loại có trường hợp sử dụng riêng. Việc chọn đúng loại thread pool có thể cải thiện đáng kể hiệu suất và độ ổn định của chương trình của bạn. Tuy nhiên, hãy nhớ rằng các loại thread khác nhau phù hợp với các tình huống ứng dụng khác nhau và chúng ta nên lựa chọn theo tình hình thực tế, bằng cách này, chúng ta có thể sử dụng nhóm luồng một cách tối đa.

### 2.3 Custom my Thread Pool

Tiếp theo là cách tạo một nhóm luồng tùy chỉnh. Mặc dù Java cung cấp một số nhóm luồng có sẵn nhưng đôi khi chúng ta cần điều chỉnh nhóm luồng của mình cho phù hợp với một tình huống cụ thể. Đây là nơi chúng ta có thể làm điều đó ThreadPoolExecutor.

ThreadPoolExecutor là class cung cấp một tập hợp các hàm tạo phong phú cho phép chúng ta kiểm soát chi tiết hoạt động của nhóm luồng, chẳng hạn như số lượng luồng, thời gian tồn tại, hàng đợi công việc, v.v.. Bây giờ, tôi sẽ cho bạn thấy cách sử dụng lớp này để tạo nhóm luồng đáp ứng nhu cầu của bạn.

![](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Miscellenous\imgs\Custom-Thread-Pool.PNG)

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue) {
    this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
         Executors.defaultThreadFactory(), defaultHandler);
}
```

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory) {
    this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
         threadFactory, defaultHandler);
}
```

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          RejectedExecutionHandler handler) {
    this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
         Executors.defaultThreadFactory(), handler);
}
```

> corePoolSize: The number of core threads, which is the number of threads that the thread pool will remain alive even if the threads are idle.
> maximumPoolSize: The maximum number of threads allowed in a thread pool.
> keepAliveTime: The lifetime of the extra idle threads when the number of threads exceeds the number of core threads.
> unit: The unit of time.keepAliveTime
> workQueue: A work queue that holds tasks to be executed.
> threadFactory: A thread factory, which is used to create threads.
> handler: Reject policy, how to handle new tasks when the thread pool and work queue are full.

```java
public class CustomThreadPoolExample {
    public static void main(String[] args) {
        int corePoolSize = 5;
        int maxPoolSize = 10;
        long keepAliveTime = 5000;
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
                corePoolSize,
                maxPoolSize,
                keepAliveTime,
                TimeUnit.MILLISECONDS,
                new LinkedBlockingQueue<Runnable>(),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.CallerRunsPolicy()
        );
        for (int i = 0; i < 20; i++) {
            executor.execute(new Task("" + i));
        }
        executor.shutdown();
    }
}

class Task implements Runnable {
    private String name;
    public Task(String name) {
        this.name = name;
    }
    public void run() {
        System.out.println("Executing : " + name);
    }
}
```

> Trong ví dụ này, tạo một nhóm luồng có 5 luồng lõi và 10 luồng tối đa. Thời gian nhàn rỗi của luồng được đặt thành 5000 mili giây, được sử dụng làm hàng đợi công việc và khi cả nhóm luồng và hàng đợi đều đầy, chính sách này sẽ được sử dụng.LinkedBlockingQueue.CallerRunsPolicy().
> Bằng cách này, chúng ta có thể tạo một nhóm luồng phù hợp nhất với nhu cầu thực tế của mình. Hãy nhớ rằng việc định cấu hình đúng các tham số của nhóm luồng là điều cần thiết để cải thiện hiệu suất và độ ổn định của chương trình của bạn.

### 2.4 Key Configurations of Thread Pools and Their Best Practices

At this point, we already know how to create a thread pool, so let's talk about the key configuration and some best practices of the thread pool. These configurations and practices can help us make better use of the thread pool and improve the performance and stability of the program.

#### Number of core threads (corePoolSize)

The number of core threads is the number of threads in the thread pool that are always active, even if they have no tasks to execute. When setting this value, we need to take into account the constraints of system resources and the actual needs of the task. If set too high, it can be a waste of system resources; If you set it too low, it may lead to a lack of processing power.

#### Maximum number of threads (maximumPoolSize)

The maximum number of threads defines the maximum number of threads that can be created by a thread pool. When the work queue is full, the thread pool starts creating new threads until this value is reached. Setting this value properly is important to prevent the system from being overloaded.

#### keepAliveTime of idle threads

When the number of threads in the thread pool exceeds the number of core threads, the redundant threads will be terminated after a certain period of time, which is the lifetime of the idle threads. This configuration helps the system free up resources when it is not busy.

#### workQueue

A work queue is used to store tasks that are waiting to be executed. The type of queue has a big impact on the behavior of the thread pool. For example, it is typically used for fixed-size thread pools, but is suitable for caching thread pools.`LinkedBlockingQueue``SynchronousQueue`

#### Reject Policy (RejectedExecutionHandler)

When both the thread pool and the work queue are full, we must define a rejection policy to handle newly joined tasks. Java provides several standard rejection strategies, such as (throwing an exception), (executing a task in the caller's thread), etc.`AbortPolicy``CallerRunsPolicy`

#### Best Practices

1. **Correctly estimate thread requirements**: Set the number of core threads and the maximum number of threads according to the nature of the task (CPU-intensive, I/O-intensive) and the system environment.
2. **Choose the right work queue**: Choose the right queue type based on the number and type of tasks.
3. **Configure a reject policy**: Select an appropriate reject policy based on your business requirements.
4. **Monitor the status of the thread pool**: Regularly monitor the status of the thread pool, including the number of threads, activity, and number of tasks, so that you can adjust the configuration in a timely manner.

Through the above configuration and practices, we can manage the thread pool more effectively and ensure the efficient and stable operation of the application. Remember, there are no one-size-fits-all rules, the key is to be flexible and adaptable to your situation.

