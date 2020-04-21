## `DateFormat` with Multiple Threads

The `DateFormat` class is not thread-safe. The `javadocs` state that "Date formats are not synchronized. It is recommended to create separate format instances for each thread. If multiple threads access a format concurrently, it must be synchronized externally."
The following code shows how you would typically use `DateFormat` to convert a String to a Date in a single-threaded environment. It is more efficient to get the format as an instance variable and use it multiple times so that the system doesn't have to fetch the information about the local language and country conventions multiple times.

```java
public class DateFormatTest {
 
  private final DateFormat format =
            new SimpleDateFormat("yyyyMMdd");
 
  public Date convert(String source)
                      throws ParseException{
    Date d = format.parse(source);
    return d;
  }
}
```
This code is not thread-safe. We can test it out by invoking the method using multiple threads. In the calling code below, I create a thread pool with 2 threads and submit 5 date conversion tasks to it. I then examine the results.

```java
final DateFormatTest t = new DateFormatTest();
Callable<Date> task = new Callable<Date>(){
    public Date call() throws Exception {
        return t.convert("20100811");
    }
};

//lets try 2 threads only
ExecutorService exec = Executors.newFixedThreadPool(2);
List<Future<Date>> results =
             new ArrayList<Future<Date>>();
 
//perform 5 date conversions
for(int i = 0 ; i < 5 ; i++){
    results.add(exec.submit(task));
}
exec.shutdown();
 
//look at the results
for(Future<Date> result : results){
    System.out.println(result.get());
}
```

When the code is run the output is unpredictable - sometimes it prints out the correct dates, sometimes the WRONG ones (`e.g.` Sat Jul 31 00:00:00 BST 2012!) and at other times it throws a `NumberFormatException!`
How can you use `DateFormat` concurrently?
There are different approaches you can take to use DateFormat in a thread-safe manner:

#### 1. Synchronization
The easiest way of making this code thread-safe is to obtain a lock on the `DateFormat` object before parsing the date string. This way only one thread can access the object at a time, and the other threads must wait.

```java
public Date convert(String source)
                    throws ParseException{
  synchronized (format) {
    Date d = format.parse(source);
    return d;
  }
}
```

#### 2. `ThreadLocals`

Another approach is to use a `ThreadLocal` variable to hold the `DateFormat` object, which means that each thread will have its own copy and doesn't need to wait for other threads to release it. This is generally more efficient than `synchronising` sa in the previous approach.

```java
public class DateFormatTest {
 
  private static final ThreadLocal<DateFormat> df
                 = new ThreadLocal<DateFormat>(){
    @Override
    protected DateFormat initialValue() {
        return new SimpleDateFormat("yyyyMMdd");
    }
  };
 
  public Date convert(String source)
                     throws ParseException{
    Date d = df.get().parse(source);
    return d;
  }
}
```

#### 3. Joda-Time

Joda-Time is a great, open-source alternative to the JDK's Date and Calendar API. It's `DateTimeFormat` is "thread-safe and immutable".

```java
import org.joda.time.DateTime;
import org.joda.time.format.DateTimeFormat;
import org.joda.time.format.DateTimeFormatter;
import java.util.Date;
 
public class DateFormatTest {
 
  private final DateTimeFormatter fmt =
       DateTimeFormat.forPattern("yyyyMMdd");
 
  public Date convert(String source){
    DateTime d = fmt.parseDateTime(source);
    return d.toDate();
  }
}
```