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