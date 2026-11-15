---
title: "Concurrency Correctness in Java: Lost Updates and the Locking Mistakes I Want to Avoid"
parent: "[[index|Ryan's Dev & Game Lab]]"
---

# Concurrency Correctness in Java: Lost Updates and the Locking Mistakes I Want to Avoid

In [[Concurrency in Java|my last post]], I walked through the basic building blocks of Java concurrency. This follow-up zooms in on the first big hurdle: **correctness**. When multiple threads touch the same data at the same time, how do we make sure the numbers actually add up?

I still have to remind myself of these basics during code reviews, so I am writing them down here while they are fresh.

## The problem: lost updates

When two threads update the same shared value at the same time, one update can quietly disappear. Picture two roommates checking the fridge, seeing one carton of milk left on the shopping list, and both ordering one more instead of coordinating. In code, both threads read the same starting value, both calculate an updated value, and whoever writes last overwrites the other person's work.

The sneaky part is that Java will not throw an exception or log an error. Your code keeps running, and your data just ends up subtly wrong.

## Approach 1: coarse-grained locking

The simplest fix is to slap `synchronized` on the whole method. Only one thread can enter at a time, so nobody can overwrite anyone else mid-calculation.

I try to default to this first when the method is short and threads rarely bump into each other. It is boring, easy to read, and hard to mess up.

The tradeoff is throughput. Think of locking the entire kitchen just so one person can grab a spoon from a drawer. Under light traffic, nobody notices. Under heavy load, every other thread gets stuck waiting in line at the door, and your app slows down.

## Two locking mistakes I try to watch out for

Even when you remember to add `synchronized`, it is surprisingly easy to leave a gap open. These are the two traps I try to double-check in my own code.

### Mistake 1: locking read and write separately

The entire read, modify, and write sequence has to sit inside **one** lock. If you lock once to read the balance, release the lock, and lock again to write the new balance, another thread can sneak right into that gap in between. Each individual step looks thread-safe, yet the combined operation still loses updates.

### Mistake 2: using different locks for the same data

A lock only works if every thread agrees on the **exact same lock object**. If Thread A locks object A while Thread B locks object B to protect the same shared map or counter, they never block each other. It is like two security guards each locking a different door to the same room while leaving the other door open.

## Where to go once traffic grows

One big lock is a great place to start, though it can turn into a bottleneck as traffic climbs. When profiling shows threads actually waiting on that lock, there are two common ways to loosen it up:
- **Fine-grained locking**: shrink the lock down to the smallest piece of data that needs protection, like how `ConcurrentHashMap` locks one bucket instead of the whole map.
- **Lock-free updates**: reach for classes like `AtomicInteger` and compare-and-swap so threads retry quickly instead of parking in line.

![[attachments/meme-distracted-coarse-locking.png]]

## Takeaway

Whenever I review code that shares state across threads, I try to run through three quick checks:
1. Does **one single lock** cover the whole read, modify, and write flow?
2. Is every thread locking on the **same object**?
3. Am I making the lock more complicated than it needs to be before I have even measured contention?

I still get tempted to jump straight to clever fine-grained or lock-free code. Most of the time, starting with one simple lock, measuring real traffic, and optimizing only the real bottleneck saves a lot of silent bugs.
