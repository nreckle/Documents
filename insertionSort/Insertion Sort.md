# Insertion Sort

![InsertionSort](InsertionSort.gif)



``` java
public static <AnyType extends Comparable<? super AnyType>>
    void insertionSort(AnyType[] items) {
        for (int next = 1; next < items.length; next++) {
            int insert;
            AnyType temp = items[next];
            for (insert = next; insert > 0 && temp.compareTo(items[insert - 1]) < 0; insert--) {
                items[insert] = items[insert - 1];
            }
            items[insert] = temp;
        }
    }
```



```java
package sorting;

public class Sort {
    public static void main(String[] args) {
        Integer[] items = {43, 1, 4, 15, 7, 90, 9, 16};
        insertionSort(items);

        for (Integer item : items) {
            System.out.printf("%d ", item);
        }
    }

    public static <AnyType extends Comparable<? super AnyType>>
    void insertionSort(AnyType[] items) {
        for (int next = 1; next < items.length; next++) {
            int insert;
            AnyType temp = items[next];
            for (insert = next; insert > 0 && temp.compareTo(items[insert - 1]) < 0; insert--) {
                items[insert] = items[insert - 1];
            }
            items[insert] = temp;
        }
    }
}
```

