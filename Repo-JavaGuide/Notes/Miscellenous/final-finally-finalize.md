## final, finally, finalize keyword trong java 

- final

Khi sử dụng final keyword để khai báo biến, có một số điều cần lưu ý sau:

```java
1. keyword final trong local variable làm cho biến trở thành một hằng số, có nghĩa là ko thể thay đổi giá trị sau khi đã khởi tạo.
2. biến final trong member variable khi được khai báo phải được gán giá trị khởi tạo ban đầu hoặc nếu ko gán giá trị ban đầu bắt buộc phải khởi tạo giá trị trong phương thức khởi tạo Constructor.
3. khi biến final được dùng làm parameter của method, có nghĩa biến đó chỉ có thể được đọc, ko thể thay đổi giá trị sau khi được gán giá trị trong lúc khởi tạo.
4. Vẫn có thể thay đổi giá trị khi khai báo với từ khoá final khi nó là một member variable và là một tham chiếu.

chú ý: 
// Notes1
Khi một method được đánh dấu là final, thì chỉ có thể được sử dụng, ko thể bị ghi đè trong Class con, tuy nhiên có thể được định nghĩa lại trong cùng Class.    
// Notes2
Khi một Class được đánh dấu final cần lưu ý
- Class được đánh dấu là final, thì không thể kế thừa, không thể có Class con.
- Các method trong Class được đánh dấu final, mặc định cũng là final method. (Class String)    
    
```

```java
// example 4
public class ExampleClass {
    final StringBuilder stringBuilder;

    public ExampleClass() {
        stringBuilder = new StringBuilder("Hello");
    }

    public void appendToStringBuilder(String str) {
        stringBuilder.append(str);
    }

    public void printStringBuilder() {
        System.out.println(stringBuilder.toString()); // Output: Hello World
    }
}
```

```java
public class Test {
    private String name;
    public final int age = 18;
    //When modified together with static, it is treated as a constant.
    private static final int HIGH = 180;

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getWeight(){
        final int weight;
        //When modifying local variables, they cannot be assigned values. The initial value needs to be given when declaring!
        return weight;
    }
    public static void main(String[] args) {
       final Test test = new Test();
       // If you reassign a reference type that is final-modified, the compiler will report an error and prompt you to cancel the final modification.
       //test = new Test();
        // But it is possible to assign attributes to this reference type!
        test.setName("JavaBuild");
        //Final modified member variables cannot be modified after the initial value is given, but they can be called
        if(test.age == 18){
            System.out.println("forever 18");
        }
    }
}
```

```java
// Notes1
public class FinalExample2 extends Animal {
    // not overriding method run(), run(String) from Animal

    @Override
    public void catRun() {
        System.out.println("FinalExample- Not cat RUN");
    }

    public static void main(String[] args) {
        FinalExample2 finalExample2 = new FinalExample2();
        finalExample2.catRun();
    }
}

class Animal {
    public final void run() {
        System.out.println("ANIMAL RUN");
    }

    // allow overloading method
    public final void run(String dog) {
        System.out.println("Animal Dog RUN");
    }

    public void catRun() {
        System.out.println("Animal Cat RUN");
    }
}
```

```java
// Notes2
public class FinalExample3 extends Zoo { // Cannot inherit from final 'xxx.finalkeyword.Zoo'
    // Class using final keyword, all method on Class auto final
}

final class Zoo {
    public void run() {
        System.out.println("Zoo run");
    }
}
```

> final: final là một từ khóa trong Java được sử dụng để chỉ định rằng một biến, một phương thức hoặc một lớp không thể thay đổi sau khi đã được khởi tạo. Ví dụ, khi một biến được đánh dấu là final, giá trị của nó không thể thay đổi sau khi đã được gán.
>
> finally: finally là một khối mã trong Java được sử dụng trong các câu lệnh try-catch-finally. Khối finally chứa các câu lệnh mà bạn muốn thực thi sau khi một khối try hoặc catch kết thúc. Các câu lệnh trong finally sẽ được thực thi dù có hoặc không có ngoại lệ xảy ra.
>
> finalize(): finalize() là một phương thức trong lớp Object của Java được sử dụng để thực hiện các tác vụ dọn dẹp hoặc giải phóng tài nguyên trước khi một đối tượng bị thu hồi bộ nhớ bởi garbage collector. Tuy nhiên, việc sử dụng finalize() không được khuyến khích vì không đảm bảo rằng phương thức sẽ được gọi trước khi một đối tượng bị thu hồi bộ nhớ.
>
> finalize() là một phương thức được định nghĩa trong lớp java.lang.Object. Phương thức finalize() trong lớp Object không làm gì cả, nhưng nó được gọi khi một đối tượng được thu hồi bởi garbage collector. Java cho phép sử dụng phương thức finalize() để thực hiện các công việc dọn dẹp cần thiết trước khi một đối tượng bị xóa khỏi bộ nhớ. Phương thức finalize() được gọi trước khi đối tượng bị xóa bởi garbage collector. Thông thường, phương thức này được gọi bởi JVM. Trong trường hợp đặc biệt, bạn có thể ghi đè phương thức finalize() để giải phóng các tài nguyên khi đối tượng được thu hồi. Trong trường hợp này, cần gọi super.finalize().

```java
public static String test() {
    String str = null;
    int i = 0;
    if (i == 0) {
        return str;//Return directly to the finally statement block that is not executed
    }
    try {
        System.out.println("try...");
        return str;
    } finally {
        System.out.println("finally...");
    }
}
 
public static String test2() {
    String str = null;
    int i = 0;
    i = i / 0;//抛出异常未执行到finally语句块
    try {
        System.out.println("try...");
        return str;
    } finally {
        System.out.println("finally...");
    }
}
 
public static String test3() {
    String str = null;
    try {
        System.out.println("try...");
        System.exit(0);//The system exits without executing the finally statement block.
        return str;
    } finally {
        System.out.println("finally...");
    }
}
```

