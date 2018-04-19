# Pass ThreadContext to another thread

Asynchronous tasks are executed on another thread. It means they cannot access to the data stored in ThreadContext. We need to decorate the ExecutorService and copy the ThreadContext to the execution thread of the asynchronous task.

```java
public class ThreadContextAwareExecutorService extends AbstractExecutorService {

    private ExecutorService executorService;

    public ThreadContextAwareExecutorService(ExecutorService executorService) {
        this.executorService = executorService;
    }

    @Override
    public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
        Map<String, String> context = ThreadContext.getContext();
        Runnable wrappedCommand = () -> {
            ThreadContext.clearMap();
            ThreadContext.putAll(context);
            command.run();
        };
        executorService.execute(wrappedCommand);
    }

    @Override
    public void shutdown() {
        executorService.shutdown();
    }

    @Override
    public List<Runnable> shutdownNow() {
        return executorService.shutdownNow();
    }

    @Override
    public boolean isShutdown() {
        return executorService.isShutdown();
    }

    @Override
    public boolean isTerminated() {
        return executorService.isTerminated();
    }

    @Override
    public boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException {
        return executorService.awaitTermination(timeout, unit);
    }
}
```

This is how we use it.

```java
public class MainApplication {
    private static final Logger logger = LogManager.getLogger(MainApplication.class);
    private static final ExecutorService executor = new ThreadContextAwareExecutorService(ForkJoinPool.commonPool());

    public static void main(String[] args) throws Exception {
        ThreadContext.put("REQUEST-ID", UUID.randomUUID().toString());
        logger.info("message 1");
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            logger.info("message 2");
            return "hello";
        }, executor);
        future.get();
    }
}
```
