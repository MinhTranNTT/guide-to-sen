### 1. How do I find duplicate data for two collections?

```java
List<String> listA = new ArrayList<>();
listA.add("Apple");
listA.add("Banana");
listA.add("Cherry");
listA.add("Date");
List<String> listB = new ArrayList<>();
listB.add("Banana");
listB.add("Date");
listB.add("Fig");
listB.add("Grape");
listA.retainAll(listB);
System.out.println("listA: " + listA);
System.out.println("listB: " + listB);
// listA: [Banana, Date]
// listB: [Banana, Date, Fig, Grape]
```

```java
origin.retainAll(listB);
```

retainAll() : phương thức này sửa đổi trên listA ban đầu, để nó chỉ chứa các phần tử có tồn tại trong cả listA và listB.

```java
List<String> collect = listA.stream().filter(listB::contains).collect(Collectors.toList());
```

tìm tập chung bằng stream().filter

```java
List<String> collect = listA.stream().filter(item -> listB.stream().anyMatch(itemB -> itemB.equals(item))).collect(Collectors.toList());
```

tìm tập chung bằng anyMatch, mỗi phần tử trong tập hợp listA có tồn tại trong listB không.

### 2. Tìm những phần tử chỉ có trong tập B

```java
List<String> collect = listB.stream().filter(a -> !listA.contains(a)).collect(Collectors.toList());
```

> In the case of a small amount of data, the method of using the Stream API is usually efficient enough and the code is concise and easy to read. If the amount of data is very large, you may want to consider other methods, such as converting collections to HashSets to make lookups more efficient, or using parallel streams to take advantage of multi-core processors.

Nếu dữ liệu ở tập A lớn hơn tập B, ta có thể xuất phát từ tập B sẽ giảm số lần duyệt qua danh sách. Ngoài ra cần tối ưu hoá các điểm sau:

- duyệt từ tập nhỏ hơn, sử dụng tập lớn hơn, giảm số lần duyệt qua danh sách.
- hiệu suất của phương pháp contains phụ thuộc vào loại Collections. Độ phức tạp của từng Collections (contains List O(n), contains HashSet O(1)).

```java
List<String> listA = Arrays.asList("Apple", "Banana", "Cherry", "Date", "Fig", "Grape");
List<String> listB = Arrays.asList("Banana", "Date", "Fig");
Set<String> setA = new HashSet<>(listA);
List<String> listC = listB.stream()
    .filter(setA::contains) 
    .collect(Collectors.toList());
List<String> listD = listB.stream()
    .filter(element -> !setA.contains(element))
    .collect(Collectors.toList());
System.out.println("List C (common elements): " + listC);
System.out.println("List D (unique to list B): " + listD);

```

> Nếu cả tập A và tập B đều được triển khai bằng cách sử dụng , thì độ phức tạp về thời gian của hai phương pháp về cơ bản là giống nhau. Mỗi lần một phương thức được gọi, một tìm kiếm tuyến tính sẽ được thực hiện trên một danh sách khác, có nghĩa là độ phức tạp về thời gian của mỗi lệnh gọi là O(n). Danh sách chứa
> Gọi cho mỗi phần tử trong tập hợp A, nếu tập hợp A có n phần tử và tập hợp B có m phần tử thì tổng độ phức tạp về thời gian là. listB::containsO(n*m)
> Gọi cho mỗi phần tử trong tập hợp B. Tương tự, nếu tập hợp A có n phần tử và tập hợp B có m phần tử thì độ phức tạp tổng thời gian cũng bằng. listA::containsO(n*m)
> Nếu tập A lớn hơn nhiều so với tập B, việc duyệt tập B nhỏ hơn thường hiệu quả hơn trong thực tế, ngay cả khi độ phức tạp về mặt lý thuyết bằng nhau. Điều này là do các bộ sưu tập nhỏ hơn được duyệt ít thường xuyên hơn, do đó làm giảm tổng số bước được thực hiện trên thực tế. Tuy nhiên, sự khác biệt về hiệu quả này chỉ có thể được nhìn thấy trong hoạt động thực tế.

### 3. Tìm kiếm một Obj trong Collection?

> Normally, we don't store strings in the collection, but some object data. How to get it at this time? Here we define one as an example, assuming that objects are considered equal when their and properties are the same.
> When using methods in Java to check whether a collection contains an object, you need to override the object's and methods. This is because the implementation of methods relies on methods to compare objects, and methods are used to quickly find and determine the location of objects in a hashed data structure (such as or).
> There is an important consistency agreement between equals and methods: hashCode
>
> If two objects are equal according to methods, then their methods must also return the same value. equalshashCode
> If two objects have different values, then they must not be equal (according to the method). hashCodeequals
>
> This convention is crucial for the correct operation of , etc. collections. If you only override the method without overriding the method, it may cause the collection to not behave as expected, for example, two objects may be considered to be different objects and store two copies even if they are equal.

```java
public class Person {
    private String name;
    private int age;
    // Constructor, getter and setter omitted

    @Override
    public boolean equals(Object o) {
        if (this == o) return true; // If it is the same object, return true directly.
        if (o == null || getClass() != o.getClass()) return false; // If the object is empty or the class type is inconsistent, return false

        Person person = (Person) o; // downward transformation
        // Compare name and age attributes
        return Objects.equals(name, person.name) && age == person.age;
    }

    @Override
    public int hashCode() {
        // Using 31 as a prime number reduces hash collisions
        int result = 17;
        result = 31 * result + Objects.hashCode(name); // Calculate hash code based on name
        result = 31 * result + Integer.hashCode(age); // Calculate hash code based on age
        return result;
    }
}
```

> In this implementation, the method first checks if it is the same object and then checks if the object is empty or of a different type. If these checks pass, it compares the properties via methods and compares the property values directly. 
> The hashCode method uses a fixed prime number (17 in this case) as the initial hash code. It then uses 31 as the multiplier (31 is a prime number often used to calculate hash codes because it helps avoid hash collisions). The methods call the and methods on the and properties respectively to calculate their hash codes and combine them.