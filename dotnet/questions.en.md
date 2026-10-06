[Русский](questions.md) | English

>## What is a deadlock?
> Frequency: sometimes

**Deadlock** — threads mutually block access to resources.
```csharp
// Thread 1
lock(A)
{
    lock(B)
    {
        
    }
}

// Thread 2
lock(B)
{
    lock(A)
    {
        
    }
}
```
**Explanation**: While **Thread #1** was doing other work, **Thread #2** locked resource B. Later, **Thread #1** locked A and tried to lock B. It cannot proceed because **Thread #2** will release B only after it acquires A.

>## What thread synchronization primitives are available?
> Frequency: sometimes

**Synchronization primitives:**
- **lock (Monitor)** — exclusive access to a resource within an application.
- **Mutex** — exclusive access within an application or across processes in the operating system.
- **Semaphore** — limits the number of threads that can access a resource simultaneously.
- **Interlocked** — atomic operations on shared variables, mainly numeric ones.

>## What is ValueTask<T>, and why is it useful?
> Frequency: often

**ValueTask\<T>** — a value-type wrapper around Task\<T> or T. It can avoid unnecessary heap allocations of Task\<T>.

>## When should you use ValueTask<T>?
> Frequency: sometimes

- When a method sometimes completes synchronously and sometimes asynchronously.
- When creating many Task<T> instances for short-lived operations, potentially tens of thousands.

>## What should you avoid doing with ValueTask<T>?
> Frequency: rarely

```csharp
// Given this ValueTask<int>-returning method…
public ValueTask<int> SomeValueTaskReturningMethodAsync() {...};

// GOOD
int result = await SomeValueTaskReturningMethodAsync();
// GOOD
int result = await SomeValueTaskReturningMethodAsync().ConfigureAwait(false);
// GOOD
Task<int> t = SomeValueTaskReturningMethodAsync().AsTask();

// WARNING: storing the instance into a local makes it much more likely it'll be misused,
// but it could still be ok
ValueTask<int> vt = SomeValueTaskReturningMethodAsync();

// BAD: awaits multiple times
ValueTask<int> vt = SomeValueTaskReturningMethodAsync();
int result = await vt;
int result2 = await vt;

// BAD: awaits concurrently (and, by definition then, multiple times)
ValueTask<int> vt = SomeValueTaskReturningMethodAsync();
Task.Run(async () => await vt);
Task.Run(async () => await vt);

// BAD: uses GetAwaiter().GetResult() when it's not known to be done
ValueTask<int> vt = SomeValueTaskReturningMethodAsync();
int result = vt.GetAwaiter().GetResult();
```

>## Why should you avoid **async void**?
> Frequency: often

- There is no Task instance for tracking completion.
- You cannot await the method.
- The caller cannot catch its asynchronous exceptions in the usual way.
- Exceptions propagate to the main execution context.

## **Why can't you use await inside lock? (2)**
> Frequency: often

The **non-Slim** synchronization primitives discussed here block the current thread while waiting for a resource, conflicting with the goal of releasing threads during asynchronous waits.
**How can you solve this?**
```csharp
private static readonly SemaphoreSlim _semaphore = new SemaphoreSlim(1, 1);

private static async Task Method1Async()
{
    await _semaphore.WaitAsync(); // Wait asynchronously for the semaphore
    try
    {
        await Task.Delay(1000); // Simulate work with the resource
    }
    finally
    {
        _semaphore.Release(); // Release after completing work with the resource
    }
}
```

>## What are I/O-bound and CPU-bound operations?
> Frequency: sometimes

**CPU-bound** — work executes on the processor, which determines its duration and keeps a thread busy.
**I/O-bound** — work involves an external device and waiting for its response.

>## How do reference types and value types differ?
> Frequency: often

**Reference type** — the variable contains a reference to an instance.
**Value type** — the variable contains the value itself.

**What is stored where?**
- Class instances are stored on the heap.
- Structs can be stored on the stack when the runtime can determine that they will not outlive their stack storage. For example, a struct used only within a method may be stack-allocated.

>## What is the CLR?
> Frequency: rarely

The **Common Language Runtime**.
**Its main responsibilities:**
- JIT compilation: converting CIL into machine code.
- The type system: type checking and compatibility.
- Exception handling in user code and the runtime.
- Memory management and garbage collection.

>## How does the Garbage Collector work?
> Frequency: rarely

**Garbage Collector** — the CLR component responsible for managing program memory.
1. **Mark phase** — identify objects reachable from roots; unreachable objects are candidates for collection.
2. **Sweep phase** — reclaim memory occupied by unreachable objects.
3. **Compact phase** — if necessary, move surviving objects closer together toward the beginning of the heap.

>## What are the benefits of GC?
> Frequency: rarely

- Automatic memory allocation and reclamation remove the need to manage:
    - Where objects are placed on the heap.
    - Which objects can be removed.
    - When and how to defragment memory.
- Memory safety removes the need to manage:
    - Which memory regions belong to which objects.
    - Whether a reference still points to a valid instance.

>## What GC modes are available?
> Frequency: sometimes

- **Workstation GC** — minimizes collection latency to keep an application responsive.
- **Server GC** — prioritizes throughput with higher resource consumption.

>## What triggers garbage collection?
> Frequency: rarely

- Low available memory.
- Exceeding the allocation budget, which can subsequently be adjusted.
- An explicit GC.Collect call.

>## What does “stop the world” mean?
> Frequency: sometimes

**Stop the world** — a pause during heap processing, such as compaction, when application execution is suspended and allocation cannot proceed.

>## How can you reduce the chance of GC in a small code region?
> Frequency: rarely

Use **GC.TryStartNoGCRegion** and **GC.EndNoGCRegion**.

>## What are unmanaged resources?
> Frequency: sometimes

**Unmanaged resources** are resources **outside the CLR's control**, such as operating-system file resources.
>## How do IDisposable and finalizers differ?
> Frequency: rarely

**.Dispose()** is called by the developer to release unmanaged resources. The final cleanup calls operating-system APIs; GC does not perform that cleanup itself.
**When it is useful:**
- Determinism: resources are released immediately; finalizer timing is unknown.
- Performance: finalizable objects require extra collection steps. They are finalized before their memory can be reclaimed by a later collection.
A **finalizer** is scheduled when GC identifies an object as eligible for collection.
**When it is useful:**
- Object lifetimes are difficult to control, for example in complex asynchronous or multithreaded scenarios.
- As a fallback when .Dispose() was not called.


>## IEnumerable<T> vs IQueryable<T>
> Frequency: sometimes

IQueryable\<T> is used for querying remote data sources, while IEnumerable\<T> represents data already in memory or computed during enumeration.