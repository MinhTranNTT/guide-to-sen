## 1. JUC?

Đa luồng luôn là một khó khăn trong quá trình phát triển Java và nó cũng là vấn đề thường xuyên xảy ra trong các cuộc phỏng vấn. Mặc dù vẫn còn thời gian nhưng tôi dự định củng cố kiến thức của mình về JUC. Tôi nghĩ rằng cơ hội có thể được tìm thấy ở khắp mọi nơi, nhưng chúng luôn được dành riêng dành cho những người đã chuẩn bị. Tôi hy vọng tất cả chúng ta có thể tiếp tục. :stuck_out_tongue_winking_eye:

Nếu chúng ta chìm rồi lại nổi lên, tôi nghĩ chúng ta sẽ trở nên khác biệt.

JUC là tên viết tắt cho bộ công cụ java.util.concurrent trong JDK. Trong gói này cung cấp nhiều đối tượng quản lý, tương tác, hỗ trợ xử lý trong môi trường đa luồng, xuất hiện kể từ JDK1.5.

## 2. Tiến trình và luồng (Process và Thread)

Process là hoạt động chạy, xử lý của một chương trình máy tính, là đơn vị cơ bản của việc phân bổ và lập kế hoạch xử lý yêu cầu dựa trên tài nguyên của hệ thống. Trong kiến trúc máy tính hiện đại, các tiến trình là nơi chứa các Thread. 

Process và Thread là 2 khái niệm quan trọng trong việc thực thi một hay nhiều chương trình trên máy tính. Tuy nhiên, nó có sự khác nhau

- Process

Process là một chương trình hoạt động trên hệ thống máy tính, được quản lý và thực thi trong không gian bộ nhớ riêng. Mỗi Process có thể chứa nhiều Thread. Process có một bộ trạng thái riêng bao gồm các thông tin như ID, trạng thái thực thi, bộ nhớ, thời gian thực hiện.

Thread là một đơn vị nhỏ nhất của việc thực thi tác vụ bên trong một Process. Một Process có thể có ít nhất 1 Thread và nhiều nhất n Thread. Thread có thể chia sẻ không gian bộ nhớ và tài nguyên với các Thread khác trong cùng một Process. Mỗi Thread có một luồng thực thi riêng, điều này cho phép nhiều tác vụ có thể được thực hiện đồng thời từ nhiều Thread bên trong 1 Process. Mỗi Thread có một trạng thái thực thi (running, ready, blocked, terminated, ...).

> Khi bạn mở một trình duyệt web, trình duyệt sẽ tạo ra một Process mới để thực thi. Process này chứa tất cả các thành phần của trình duyệt, bao gồm các tab, cửa sổ, và tiến trình Javascript.
>
> Trong Process của trình duyệt, mỗi tab hoạt động như một Thread riêng biệt. Mỗi tab có thể tải một trang web hoặc thực hiện một công việc khác nhau, và chúng chia sẻ tài nguyên với các tab khác trong cùng một trình duyệt.

## 3. Các các phổ biến để tạo Thread trong java

### 3.1 Tạo một Thread bằng cách implements Runnable

```java
public class TheardCreateDemo {
    public static void main(String[] args) {
        Runnable runnable = new SomeRunnable();
        Thread thread1 = new Thread(runnable);
        thread1.start();
        Thread thread2 = new Thread(() -> {
            System.out.println("Use lambda expression method");
        });
        thread2.start();
    }
}
class SomeRunnable implements Runnable
{
    @Override
    public void run()
    {
        System.out.println(Thread.currentThread().getName()+":: Create a Thread thread by implementing the Runnable interface");
    }
}
```

### 3.2 Tạo một Thread mới bằng cách extends Thread

```java
public class TheardCreateDemo {
    public static void main(String[] args) {
        SomeThread thread = new SomeThread();
        thread.start();
    }
}
class SomeThread extends Thread{
    @Override
    public void run() {
        System.out.println("Create a thread by inheriting the Thread class");
    }
}
```

### 3.3 Tạo một Thread bằng cách implements Callable

```java
public class TheardCreateDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<Object> futureTask = new FutureTask<Object>(new SomeCallable<Object>());
        Thread oneThread = new Thread(futureTask);
        oneThread.start();
        // Here you can get the return value after running
        System.out.println(futureTask.get());
    }
}
class SomeCallable<Object> implements Callable<Object> {
    @Override
    public Object call() throws Exception {
        System.out.println("Create a Thread thread by implementing the Callable interface");
        // This can return data. Just return 1024 here. Haha
        return (Object)" This can return data. Just return it casually.";
    }
}
```

* Notes:

Create a class SomeCallable that implements the Callable interface

Create a class object: Callable oneCallable = new SomeCallable();

Create a FutureTask object from Callable: FutureTask futureTask= new FutureTask(oneCallable);

Note: FutureTask is a wrapper that is created by accepting Callable, which implements both Future and Runnable interfaces.
Step 4: Create a Thread object from FutureTask: Thread oneThread = new Thread(futureTask);

Start the thread: oneThread.start();

Why does Thread(FutureTask futureTask) work here?

```java
public Thread(Runnable target) {
    this(null, target, "Thread-" + nextThreadNum(), 0);
}

public class FutureTask<V> implements RunnableFuture<V> 
  
public interface RunnableFuture<V> extends Runnable, Future<V> 
```

## 4. Đồng thời và Song song và Tuần tự

Tuần tự: tất cả các tác vụ được thực hiện theo một thứ tự. Chỉ có thể thực hiện 1 tác vụ tại 1 thời điểm.

ví dụ: Mua vé ga tàu, chỉ có 1 phòng vé, ai đến trước phục vụ trước, một cách tuần tự.

Song song: có thể nhận nhiều tác vụ cùng một lúc và các tác vụ được xử lý cùng một lúc. Nhưng được hoạt động bằng cách sử dụng sức mạnh từ phần cứng vật lý (tác vụ được thực hiện song song trên CPU)

ví dụ: Mua vé ga tàu, nhưng số lượng phòng vé là 5, lúc này nó tương đương với việc chia hàng đợi thành nhiều hàng đợi nhỏ hơn. Phụ thuộc vào sức phần mạnh phần cứng. 

Đồng thời: Trong cùng khoảng thời gian, nhận được nhiều tác vụ cùng lúc và các tác vụ được thi chồng chéo nhau về mặt thời gian. 2 hoặc nhiều chương trình có thể cùng chạy và hoàn thành trong khoảng thời gian chồng chéo nhau, điều này ko nhất thiết cả 2 chạy cùng lúc (đa nhiệm trên lõi đơn)

> **Concurrency** is when two or more tasks can start, run, and complete in overlapping time **periods**. It doesn't necessarily mean they'll ever both be running **at the same instant**. For example, *multitasking* on a single-core machine.
>
> **Parallelism** is when tasks *literally* run at the same time, e.g., on a multicore processor.

> # Why the Confusion Exists
>
> Confusion exists because dictionary meanings of both these words are almost the same:
>
> - **Concurrent**: existing, happening, or done at the same time(dictionary.com)
> - **Parallel**: very similar and often happening at the same time(merriam webster).
>
> Yet the way they are used in computer science and programming are quite different. Here is my interpretation:
>
> - **Concurrency**: Interruptability
> - **Parallelism**: Independentability
>
> **So what do I mean by above definitions?**
>
> I will clarify with a real world analogy. Let’s say you have to get done 2 very important tasks in one day:
>
> 1. Get a passport
> 2. Get a presentation done
>
> Now, the problem is that task-1 requires you to go to an extremely bureaucratic government office that makes you wait for 4 hours in a line to get your passport. Meanwhile, task-2 is required by your office, and it is a critical task. Both must be finished on a specific day.
>
> ## Case 1: Sequential Execution
>
> Ordinarily, you will drive to passport office for 2 hours, wait in the line for 4 hours, get the task done, drive back two hours, go home, stay awake 5 more hours and get presentation done.
>
> ## Case 2: Concurrent Execution
>
> But you’re smart. You plan ahead. You carry a laptop with you, and while waiting in the line, you start working on your presentation. This way, once you get back at home, you just need to work 1 extra hour instead of 5.
>
> In this case, both tasks are done by you, just in pieces. You interrupted the passport task while waiting in the line and worked on presentation. When your number was called, you interrupted presentation task and switched to passport task. The saving in time was essentially possible due to interruptability of both the tasks.
>
> Concurrency, IMO, can be understood as the "isolation" property in [ACID](https://stackoverflow.com/q/3740280/3635931). Two database transactions are considered isolated if sub-transactions can be performed in each and any interleaved way and the final result is same as if the two tasks were done sequentially. Remember, that for both the passport and presentation tasks, *you are the sole executioner*.
>
> ## Case 3: Parallel Execution
>
> Now, since you are such a smart fella, you’re obviously a higher-up, and you have got an assistant. So, before you leave to start the passport task, you call him and tell him to prepare first draft of the presentation. You spend your entire day and finish passport task, come back and see your mails, and you find the presentation draft. He has done a pretty solid job and with some edits in 2 more hours, you finalize it.
>
> Now since, your assistant is just as smart as you, he was able to work on it *independently*, without needing to constantly ask you for clarifications. Thus, due to the independentability of the tasks, they were performed at the same time by *two different executioners*.
>
> *Still with me? Alright...*
>
> ## Case 4: Concurrent But Not Parallel
>
> Remember your passport task, where you have to wait in the line? Since it is **your** passport, your assistant cannot wait in line for you. Thus, the passport task has *interruptability* (you can stop it while waiting in the line, and resume it later when your number is called), but no *independentability* (your assistant cannot wait in your stead).
>
> ## Case 5: Parallel But Not Concurrent
>
> Suppose the government office has a security check to enter the premises. Here, you must remove all electronic devices and submit them to the officers, and they only return your devices after you complete your task.
>
> In this, case, the passport task is neither *independentable* nor *interruptible*. Even if you are waiting in the line, you cannot work on something else because you do not have necessary equipment.
>
> Similarly, say the presentation is so highly mathematical in nature that you require 100% concentration for at least 5 hours. You cannot do it while waiting in line for passport task, even if you have your laptop with you.
>
> In this case, the presentation task is *independentable* (either you or your assistant can put in 5 hours of focused effort), but not *interruptible*.
>
> ## Case 6: Concurrent and Parallel Execution
>
> Now, say that in addition to assigning your assistant to the presentation, you also carry a laptop with you to passport task. While waiting in the line, you see that your assistant has created the first 10 slides in a shared deck. You send comments on his work with some corrections. Later, when you arrive back home, instead of 2 hours to finalize the draft, you just need 15 minutes.
>
> This was possible because presentation task has *independentability* (either one of you can do it) and *interruptability* (you can stop it and resume it later). So you concurrently executed *both* tasks, and executed the presentation task in parallel.
>
> Let’s say that, in addition to being overly bureaucratic, the government office is corrupt. Thus, you can show your identification, enter it, start waiting in line for your number to be called, bribe a guard and someone else to hold your position in the line, sneak out, come back before your number is called, and resume waiting yourself.
>
> In this case, you can perform both the passport and presentation tasks concurrently and in parallel. You can sneak out, and your position is held by your assistant. Both of you can then work on the presentation, etc.
>
> ------
>
> # Back to Computer Science
>
> In computing world, here are example scenarios typical of each of these cases:
>
> - **Case 1:** Interrupt processing.
> - **Case 2:** When there is only one processor, but all executing tasks have wait times due to I/O.
> - **Case 3:** Often seen when we are talking about map-reduce or hadoop clusters.
> - **Case 4:** I think Case 4 is rare. It’s uncommon for a task to be concurrent but not parallel. But it *could* happen. For example, suppose your task requires access to a special computational chip that can be accessed through only processor-1. Thus, even if processor-2 is free and processor-1 is performing some other task, the special computation task cannot proceed on processor-2.
> - **Case 5:** also rare, but not quite as rare as Case 4. A non-concurrent code can be a critical region protected by mutexes. Once it is started, it *must* execute to completion. However, two different critical regions can progress simultaneously on two different processors.
> - **Case 6:** IMO, most discussions about parallel or concurrent programming are basically talking about Case 6. This is a mix and match of both parallel and concurrent executions.

```te
Concurrency                 Concurrency + parallelism
(Single-Core CPU)           (Multi-Core CPU)
 ___                         ___ ___
|th1|                       |th1|th2|
|   |                       |   |___|
|___|___                    |   |___
    |th2|                   |___|th2|
 ___|___|                    ___|___|
|th1|                       |th1|
|___|___                    |   |___
    |th2|                   |   |th2|
```

![](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Concurrency\imgs\OdYWr.gif)

![RRF1J](D:\MinhTran-Miscellenous\Repo-JavaGuide\Notes\Concurrency\imgs\RRF1J.gif)

## 5. Luồng người dùng và luồng deamon

User Thread

Là thread mà hđh trực tiếp quản lý và có thể tương tác trực tiếp với người dùng thông qua các tiến trình (process)

Mỗi User Thread hoạt động độc lập với các Thread khác trong ứng dụng, nhưng chúng có thể chia sẻ chung các tài nguyên bộ nhớ và tài nguyên hệ thống. 

Deamon Thread:

là một loại Thread đặc biệt ko phụ thuộc vào hoạt động của người dùng và thường chạy trong nền (background) của hệ thống mà ko yêu cầu sự tương tác từ phía người dùng.

> Dưới đây là một số ví dụ cụ thể về việc sử dụng luồng người dùng và luồng daemon trong các ứng dụng và hệ thống thực tế:
>
> 1. **Luồng Người Dùng:**
>    - Trong một ứng dụng đồng bộ hóa dữ liệu từ nhiều nguồn (ví dụ: ứng dụng gửi và nhận tin nhắn), mỗi kết nối có thể được quản lý bằng một luồng người dùng riêng biệt. Điều này giúp ứng dụng không bị chặn khi một kết nối đang chờ đợi dữ liệu.
>    - Trong trò chơi trực tuyến, mỗi người chơi có thể được xử lý trong một luồng người dùng riêng, giúp tối ưu hóa hiệu suất và tránh tình trạng lag cho người chơi.
> 2. **Luồng Daemon:**
>    - Một dịch vụ web server như Apache hoặc Nginx chạy như một luồng daemon để phục vụ các yêu cầu HTTP từ các máy khách mà không cần sự can thiệp của người dùng.
>    - Hệ thống quản lý gói như apt (trên Ubuntu) hoặc yum (trên CentOS) có thể chạy như các luồng daemon, tự động kiểm tra và tải xuống các gói cập nhật mới từ các kho lưu trữ mà không cần sự tương tác của người dùng.
>
> Những ví dụ này chỉ ra rằng luồng người dùng thường được sử dụng trong các tác vụ đòi hỏi tương tác với người dùng hoặc đòi hỏi đồng bộ hóa, trong khi luồng daemon thường được sử dụng để thực hiện các tác vụ tự động trong nền mà không cần sự can thiệp của người dùng.

```java
public static void main2(String[] args) {
   Thread thread = new Thread(() -> {
       System.out.println("not setting deamon thread");
       while (true) {

       }
   });
   thread.start();
   System.out.println("DONE");
}

public static void main(String[] args) {
    Thread thread = new Thread(() -> {
        System.out.println("setting is deamon thread");
        while (true) {

        }
    });
    thread.setDaemon(true);
    thread.start();
    System.out.println("DONE");
}
```

> Đoạn mã bạn cung cấp định nghĩa hai phương thức main2 và main. Cả hai đều tạo ra một luồng mới và in ra một thông điệp sau đó vô hạn lặp. Tuy nhiên, sự khác biệt chính đến từ cách xử lý luồng đa nhiệm:
>
> 1. **Phương thức main2:**
>    - Trong phương thức này, một luồng mới được tạo ra và khởi chạy, nhưng không được đặt là luồng daemon (daemon thread). Điều này có nghĩa là khi chương trình chính (main thread) kết thúc, luồng mới tạo này vẫn tiếp tục chạy, không giữa kết thúc chương trình.
>    - Đoạn mã này sẽ in ra "DONE" ngay sau khi luồng mới được khởi chạy, sau đó chương trình kết thúc và luồng mới vẫn tiếp tục chạy vô hạn.
> 2. **Phương thức main:**
>    - Trong phương thức này, một luồng mới cũng được tạo ra và khởi chạy, nhưng lần này được đặt là luồng daemon bằng cách gọi phương thức setDaemon(true) trước khi bắt đầu luồng.
>    - Khi chương trình chính kết thúc, các luồng daemon sẽ tự động kết thúc ngay lập tức mà không cần chờ các hoạt động hoặc luồng còn lại. Do đó, sau khi in ra "DONE", chương trình chính kết thúc, và luồng daemon cũng kết thúc ngay lập tức, không cần chờ đến khi các hoạt động khác hoàn thành.
>
> Tóm lại, sự khác biệt chính giữa hai phương thức này là trong cách xử lý luồng đa nhiệm: một là không đặt luồng là luồng daemon và một là đặt luồng là luồng daemon.

(In vernacular: It is to guard the user thread. When the user thread dies, the guard thread will also die.)

Hello, I am the MinhTran. I am a small seed on the road to Java learning. I also hope that one day it will take root and grow into a big tree.

[Concurrency vs. Parallelism (jenkov.com)](https://jenkov.com/tutorials/java-concurrency/concurrency-vs-parallelism.html)