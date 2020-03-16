# [Difference between shutdown and shutdownNow of Executor Service](https://stackoverflow.com/questions/11520189/difference-between-shutdown-and-shutdownnow-of-executor-service)

I want to know the basic difference between `shutdown()` and `shutdownNow()` for shutting down the `Executor Service` ?As far as I understood `shutdown()` should be used for **graceful** shutdown which means all tasks that were runing and queued for processing but not started should be allowed to complete and `shutdownNow()` does an **abrupt** shut down meaning that some unfinished tasks are cancelled and unstarted tasks are also cancelled . Is there anything else which is implicit/explicit that I am missing ?

P.S: I found another question on [SO](https://stackoverflow.com/questions/10504172/how-to-shutdown-an-executor-service) related to this but not exactly what I want know .



In summary, you can think of it that way:

- `shutdown()` will just tell the executor service that it can't accept new tasks, but the already submitted tasks continue to run
- `shutdownNow()` will do the same AND will **try to cancel** the already submitted tasks by interrupting the relevant threads. Note that if your tasks ignore the interruption, `shutdownNow` will behave exactly the same way as `shutdown`.

You can try the example below and replace `shutdown` by `shutdownNow` to better understand the different paths of execution:

- with `shutdown`, the output is `Still waiting after 100ms: calling System.exit(0)...` because the running task is **not** interrupted and continues to run.
- with `shutdownNow`, the output is `interrupted` and `Exiting normally...` because the running task is interrupted, catches the interruption and then stops what it is doing (breaks the while loop).
- with `shutdownNow`, if you comment out the lines within the while loop, you will get `Still waiting after 100ms: calling System.exit(0)...` because the interruption is not handled by the running task any longer.

```java
public static void main(String[] args) throws InterruptedException {
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.submit(new Runnable() {

        @Override
        public void run() {
            while (true) {
                if (Thread.currentThread().isInterrupted()) {
                    System.out.println("interrupted");
                    break;
                }
            }
        }
    });

    executor.shutdown();
    if (!executor.awaitTermination(100, TimeUnit.MICROSECONDS)) {
        System.out.println("Still waiting after 100ms: calling System.exit(0)...");
        System.exit(0);
    }
    System.out.println("Exiting normally...");
}
```