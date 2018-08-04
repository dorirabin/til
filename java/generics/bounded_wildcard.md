# Bounded wildcard

## Covariance

Suppose we have this piece of code to get the sum of amounts for all orders.

```java
public class Order {
  private double amount;

  Order(double amount) { this.amount = amount; }

  public double getAmount() { return amount; }
}

public class Calculator {
  public double sum(List<Order> orders) {
    return orders
            .stream()
            .map(order -> order.amount)
            .reduce((a, b) -> a + b)
            .orElse(0d);
  }
}

public class Application {
  public static void main(String[] args) {
    Calculator calculator = new Calculator();

    List<Order> orders = new ArrayList<>();
    orders.add(new Order(1));
    orders.add(new Order(2));
    double total = calculator.sum(orders);

    System.out.println(total);
  }
}
```

One day, we want to have a new type of order for sales.

```java
public class SalesOrder extends Order {
  SalesOrder(double amount) { super(amount * 0.7); }
}

public class Application {
  public static void main(String[] args) {
    Calculator calculator = new Calculator();

    List<SalesOrder> salesOrders = new ArrayList<>();
    salesOrders.add(new SalesOrder(1));
    salesOrders.add(new SalesOrder(2));
    double total = calculator.sum(salesOrders); // compilation error

    System.out.println(total);
  }
}
```

Java compiler complains that you cannot assign `List<SalesOrder>` instance to `List<Order>` type variable, i.e. `List<Order> orders = salesOrders`.

In order to allow us to accept `List<SalesOrder>` instance, we can add a upper bounded wildcard by changing `List<Order>` to `List<? extends Order>`.

```java
public class Calculator {
  public double sum(List<? extends Order> orders) {
      return orders
              .stream()
              .map(order -> order.getAmount())
              .reduce((a, b) -> a + b)
              .orElse(0d);
  }
}
```

`Covariance` is `Enables you to use a more derived type than originally specified.` [1]

`order` parameter is a `producer` which provides item for `sum` method to consume.

## Contravariance

Suppose we have this piece of code for our clients.

```java
class Person {
    String id;
    Person(String id) { this.id = id; }
}

class Student extends Person {
    LocalDate joiningDate;
    Student(String id, LocalDate joiningDate) {
        super(id);
        this.joiningDate = joiningDate;
    }
}

class StudentDao {
    void findAll(List<Student> students) {
        students.add(new Student("1", LocalDate.of(2016, 9, 1)));
        students.add(new Student("2", LocalDate.of(2017, 4, 1)));
    }
}
```

One client (Client A) uses `List<Student>`.

```java
public static void main(String[] args) {
    List<Student> students = new ArrayList<>();
    StudentDao studentDao = new StudentDao();
    studentDao.findAll(students);
}
```

Another client (Client B) uses `List<Person>` but they have compilation error, becuase they are trying to assign `List<Person>` instance to `List<Student>` type variable.

```java
public static void main(String[] args) {
    List<Person> personList = new ArrayList<>();
    StudentDao studentDao = new StudentDao();
    studentDao.findAll(personList); // compilation error
}
```

Suppose these people are our clients and it is difficult for them to change the code. Then we need to think about how to change on our side.

First, we try to modify in following way. Although client B can compile their code, client A will have compilation error becuase of assigning `List<Student>` instance to `List<Person>` type variable. (Same problem we got in `Covariance` section)

```java
class StudentDao {
  void findAll(List<Person> students) {
      students.add(new Student("1", LocalDate.of(2016, 9, 1)));
      students.add(new Student("2", LocalDate.of(2017, 4, 1)));
  }
}
```

This time, we need to add a lower bounded wildcard. We change to `List<? super Student>`.

```java
class StudentDao {
  void findAll(List<? super Student> students) {
      students.add(new Student("1", LocalDate.of(2016, 9, 1)));
      students.add(new Student("2", LocalDate.of(2017, 4, 1)));
  }
}
```

`Contravariance` is `Enables you to use a more generic (less derived) type than originally specified.` [1]

`students` parameter is a `consumer` which takes item from `findAll` method.

## Extra

Joshua Bloch (Author of Effective Java) gives a mnemonic `Producer extends Consumer super` (PECS) to memorize when to use `extends` and `super` for bounded wildcard.

## Reference

[1] https://docs.microsoft.com/en-us/dotnet/standard/generics/covariance-and-contravariance
