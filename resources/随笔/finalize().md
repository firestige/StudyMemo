# 关于finalize()

finalize()并不保证执行，GC时只会尝试调用一次，且不保证该方法一定获得执行。因此利用finalize()进行关资源等操作是不可靠的。然而，Finalize()可以被用于对象中介条件的验证，例子如下：

```java
class Book {
    boolean checkedOut = false;
    Book(boolean checkOut) {
        checkedOut = checkOut;
    }
    void checkIn() {
        checkedOut = false;
    }
    protected void finalize() {
        if (checkedOut) {
            System.out.println("Error: checked out")
            super.finalize();
        }
    }
}

public class TerminationCondition {
    public static void main (String[] args) {
        Book novel = new Book(true);
        novel.checkIn();
        new Book(true)
        System.gc();
    }
}

/**
 * Output:
 * Error: checked Out
 */
```