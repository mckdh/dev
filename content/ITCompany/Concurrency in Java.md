---
title: "Concurrency in Java: What I Finally Understood After Years of Pretending"
parent: "[[index|Ryan's Dev & Game Lab]]"
---
I have been writing code for a long time, and for years I did something a little embarrassing. Whenever concurrency came up in a meeting or code review, I nodded along without really understanding how the pieces fit together.

I still catch myself glossing over thread safety when I am rushing to finish a ticket. So I wrote this note for myself, as a simple mental model I can come back to when things get fuzzy.

## 1. What concurrency actually means

Think about a busy kitchen. If one cook puts a pot of water on the stove and chops onions while waiting for it to boil, that is **concurrency**. One person is juggling multiple tasks so nothing sits idle. **Parallelism** is hiring a second cook so two dishes are actually cooked on two stoves at the exact same time.

People often say, "only one thread should run this method at a time," which is a little misleading. Two threads can run the exact same method all day without any trouble, as long as they work on separate data. What actually needs protection is the **shared data** they both read and write.

## 2. Tasks and threads

In Java, you can wrap a piece of work in a `Runnable`, pass it to a `Thread`, and call `start()`. That works fine for a small script.

The catch is that spinning up a brand new thread for every single request is expensive. A traditional Java thread maps to an operating system thread and eats up memory and setup time. If thousands of requests hit your server at once and you create a new thread for each one, you will burn through memory fast.

## 3. Thread pools

Instead of hiring a new temp worker for every five-second errand and firing them right after, it makes more sense to keep a small crew on standby.

That is what a **thread pool** (`ExecutorService`) does. You create a fixed number of threads once, hand them a queue of tasks, and let the pool reuse those same workers over and over.

## 4. `volatile`: visibility, not atomicity

Modern CPUs are much faster than RAM. To avoid waiting on slow memory trips, each CPU core keeps its own small scratchpad (the L1 and L2 cache).

That shortcut creates a sneaky bug. Thread A might update a variable on its own scratchpad, while Thread B keeps reading the old value from its own scratchpad and never sees the change. It is like two cooks writing updates on personal sticky notes instead of looking at the main kitchen whiteboard.

Marking a variable `volatile` fixes this **visibility** problem. It tells Java: do not let threads rely on stale local copies; read from and write to main memory so everyone sees the latest value right away. It also stops the compiler and CPU from reordering certain instructions around that variable.

Here is the trap I still have to remind myself about: `volatile` does **not** make compound steps safe. Even on a `volatile` field, `counter++` can still lose updates. Under the hood, `counter++` is three separate steps: read the number, add one, and write it back. If two threads both read `5` at the same time, they will both write `6`, and one increment quietly disappears.

![[attachments/meme-this-is-fine-volatile-counter.png]]

## 5. Atomics and compare-and-swap

When I just need a safe counter or flag without heavy locking, classes like `AtomicInteger` are my go-to. They rely on a hardware trick called **compare-and-swap (CAS)**.

Instead of locking the door so nobody else can enter, CAS works like an optimistic check:
1. Read the current value (say, `5`).
2. Calculate the new value (`6`).
3. Write `6` back only if the number in memory is still `5`.

If another thread sneaked in and changed it to `7` first, your write fails. You simply grab the fresh `7`, recalculate `8`, and try again until it sticks.

## 6. `ConcurrentHashMap`

A regular synchronized map puts one giant padlock on the entire map. Only one thread can touch it at a time, even if two threads care about completely different keys.

`ConcurrentHashMap` avoids that bottleneck by shrinking the lock down to individual buckets, like locking one drawer in a filing cabinet instead of locking the whole room. When you insert into an empty bucket, it uses CAS with no lock at all. If the bucket already has items, it locks only that single bucket (using `synchronized` on the first node) while other threads keep working freely on other buckets.

## 7. Deadlock

A **deadlock** is what happens when two threads freeze up waiting on each other forever. Think of two people meeting in a narrow hallway where neither steps aside, or two people trying to eat when each holds one chopstick and waits for the other person's chopstick.

In code, Thread A grabs Lock 1 and waits for Lock 2, while Thread B holds Lock 2 and waits for Lock 1. Neither can move, and your app just hangs.

The simplest way to prevent this is boring but reliable: make sure every thread acquires locks in the **exact same order**. If everyone always asks for Lock 1 before Lock 2, that circle can never form.

## 8. `ReentrantLock` and `tryLock`

With `synchronized`, if a thread cannot get a lock, it sits there waiting until the lock opens up, even if that takes forever.

`ReentrantLock` gives you an escape hatch called `tryLock` with a timeout. You can say, "try to grab this lock for 500 milliseconds, and if it is still busy, give up and return `false`." That timeout makes it much easier to recover instead of hanging in a deadlock. Just remember to always put `unlock()` inside a `finally` block, because if an exception throws before you release the lock, nobody else will ever get in.

## 9. `CompletableFuture`

Sometimes you are not fighting over shared counters; you just want slow tasks (like calling three external APIs) to run without freezing the thread.

`CompletableFuture` is like getting a restaurant buzzer after placing an order instead of standing frozen at the counter until your food is ready. You can kick off a background call, chain the next step when a result comes back, or handle errors without blocking the caller.

## 10. Three questions I try to ask myself

Whenever concurrent code starts feeling overwhelming, I try to sort the problem into three buckets:
- **[[Concurrency Correctness in Java|Correctness]]**: Is the shared data safe from stale reads or half-finished updates? (`volatile`, atomics, locks)
- **Coordination**: Are tasks handed off and waited on cleanly? (thread pools, `CompletableFuture`)
- **Liveness**: Can the program keep moving forward without getting stuck? (consistent lock order, `tryLock` timeouts)

## Takeaway

Concurrency stopped feeling quite so intimidating once I stopped trying to memorize every Java keyword and focused on the basics: where data is shared, and how threads wait on each other.

I still make mistakes here and have to look things up when I am tired or rushing a release. Whenever I get lost in a bug, I try to start with one question: *who else can touch this piece of data right now?* Usually, that points me straight to the real issue.

Next up: [[Concurrency Correctness in Java|Concurrency Correctness in Java: Lost Updates and the Locking Mistakes I Want to Avoid]].
