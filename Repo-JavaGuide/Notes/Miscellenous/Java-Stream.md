## 1. Tổng quan về Java Stream

Stream là gì? những đặc điểm và ưu điểm chính của nó

Stream là gì? Stream là một tính năng mới được giới thiệu trong JDK 1.8, được sử dụng để thực hiện các thao tác trên dữ liệu của tập hợp. Nó cung cấp một cách tiếp cận đơn giản và hiệu quả hơn để xử lý dữ liệu của tập hợp, cho phép chuyển đổi các thao tác trên tập hợp thành một chuỗi các thao tác tuần tự, giúp thực hiện các công việc xử lý dữ liệu một cách mạnh mẽ và dễ tiếp cận hơn, Stream dự trên triết lý lập trình hàm.

Đặc điểm và ưu điểm chính:

- Gọi chuỗi: Stream hỗ trợ việc gọi chuỗi, cho phép nối nhiều thao tác lại với nhau, tạo thành một quy trình xử lý dữ liệu liên tục, làm cho mã trở nên rõ ràng và dễ đọc hơn.

- Lazy evaluation: Stream sử dụng chiến lược đánh giá lười biếng, chỉ có khi thao tác terminal được gọi thì các thao tác trung gian mới được thực hiện, điều này giúp tăng hiệu suất, tránh tính toán không cần thiết.

- Xử lý song song: Stream cung cấp khả năng xử lý song song, cho phép tự động thực hiện tính toán song song khi xử lý dữ liệu quy mô lớn, tận dụng hiệu suất của bộ xử lý đa lõi, tăng hiệu suất thực thi chương trình

- Lập trình hàm: Stream thúc đẩy triết lý lập trình hàm, cho phép định nghĩa các thao tác thông qua biểu thức Lambda, giúp giảm thiểu mã lặp và làm cho mã nguồn trở nên ngắn gọn hơn.

```java

public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        // 1. Chain call
        List<Integer> squaredNumbers = numbers.stream()
                                               .map(n -> n * n)
                                               .collect(Collectors.toList());
        System.out.println("Squared numbers: " + squaredNumbers);

        // 2. Lazy evaluation
        List<Integer> evenNumbers = numbers.stream()
                                           .filter(n -> {
                                               System.out.println("Filtering " + n);
                                               return n % 2 == 0;
                                           })
                                           .collect(Collectors.toList());
        System.out.println("Even numbers: " + evenNumbers);

        // 3. parallel processing
        long count = numbers.parallelStream()
                           .filter(n -> {
                               System.out.println("Filtering " + n);
                               return n % 2 == 0;
                           })
                           .count();
        System.out.println("Count of even numbers: " + count);

        // 4. functional programming
        int sum = numbers.stream()
                         .reduce(0, Integer::sum);
        System.out.println("Sum of numbers: " + sum);
    }
```

> Trong ví dụ trên:
>
> 1. Chuỗi gọi: Chúng ta sử dụng phương thức `stream()` để tạo ra một Stream từ danh sách các số nguyên. Sau đó, chúng ta sử dụng phương thức `map()` để ánh xạ mỗi số sang bình phương của nó. Kết quả là một danh sách mới chứa các số đã bình phương.
> 2. Lazy evaluation: Trong phương thức `filter()`, chúng ta in ra mỗi số trước khi kiểm tra xem số đó có phải là số chẵn không. Tuy nhiên, các phương thức của Stream chỉ thực sự được gọi khi phương thức terminal (`collect()` trong trường hợp này) được gọi. Do đó, thông điệp "Filtering" chỉ được in ra cho các số thỏa mãn điều kiện.
> 3. Xử lý song song: Chúng ta sử dụng phương thức `parallelStream()` để tạo ra một Stream song song. Các phép kiểm tra số chẵn được thực hiện song song trên các luồng khác nhau, tận dụng hiệu suất của bộ xử lý đa lõi.
> 4. Lập trình hàm: Trong phương thức `reduce()`, chúng ta sử dụng phương thức thức hợp (lambda expression) để tính tổng của tất cả các số trong danh sách. Điều này giúp giảm bớt mã lặp và làm cho mã nguồn trở nên ngắn gọn hơn.

```java
List<String> fruits = Arrays.asList("Apple","Banana","Cherry","Date","Elderberry");
fruits.stream().filter(fruit -> fruit.length > 5).forEach(System.out::println);
fruits.stream().map(String::toUpperCase).forEach(System.out::println);
fruits.stream().sorted().forEach(System.out::println);
fruits.stream().reduce("", (partialResult, fruit) -> partialResult + " " + fruit);
```

