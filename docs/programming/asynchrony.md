## Overview

This tutorial will introduce the concept of asynchrony in programming. It will also provide some Python examples on how to do this practically.

## What is asynchrony?

From the first paragraph of [Wikipedia](https://en.wikipedia.org/wiki/Asynchrony_(computer_programming)) on the topic:

> Asynchrony, in computer programming, refers to the occurrence of events independent of the main program flow and ways to deal with such events. These may be "outside" events such as the arrival of signals, or actions instigated by a program that take place concurrently with program execution, without the program hanging to wait for results. Asynchronous input/output is an example of the latter case of asynchrony, and lets programs issue commands to storage or network devices that service these requests while the processor continues executing the program. Doing so provides a degree of parallelism. - Wikipedia

The key idea here is that with asynchrony we have a way of distributing tasks in way such that we prevent blocking the main loop of our program. This is especially useful for when a subroutine relies on responses from another operation that takes place outside of our main loop.

For example, say we are doing some data mining, sending requests to an API. In traditional synchronous programming, we would send request 1, wait for response 1, then send request 2, and wait for response 2. What if, response 2 is significantly smaller in size or uses a different endpoint that is faster than the endpoint used in request 1? Rather than waiting for response 1, consequently blocking the main loop of our routine, we'd want to grab response 2 while we wait and possibly do some other operations.

> A common way for dealing with asynchrony in a programming interface is to provide subroutines that return a future or promise that represents the ongoing operation, and a synchronizing operation that blocks until the future or promise is completed. - Wikipedia

<figure markdown="span">
  ![Sync v Async](imgs/sync_vs_async.svg){ width=600 }
  <figcaption>A diagram demonstrating the difference between synchronous and asynchronous requests.</figcaption>
</figure>

## Asyncio

The Python Standard Library provides a package for doing this easily called [asyncio](https://docs.python.org/3/library/asyncio.html). We will look at a couple basic examples of using asyncio. Credits to [realpython](https://realpython.com/async-io-python/) for some inspiration for the examples.

=== "Asynchronous"

    ``` py
    # count_async.py

    import asyncio, time

    async def count(): # (1)!
        print("One")
        # mimics some operation
        await asyncio.sleep(1) # (2)!
        print("Two")

    async def main():
        tasks = [count(), count(), count()]
        await asyncio.gather(*tasks) # (3)!

    if __name__ == "__main__":
        s = time.perf_counter()
        asyncio.run(main()) # (4)!
        elapsed = time.perf_counter() - s
        print(f"{__file__} executed in {elapsed:0.2f} seconds.")
    ```

    1. Notice the `async` keyword here. It denotes that the function is an asynchronous routine.
    2. Notice the `await` keyword here. This suspends the execution of the coroutine until the awaitable it takes completes and returns a result.
    3. `gather` runs [awaitable objects](https://docs.python.org/3/library/asyncio-task.html#asyncio-awaitables) in the *tasks* sequence *concurrently*.
    4. `run` executes the coroutine *main* and returns the result.

=== "Synchronous"

    ``` py
    # count_sync.py

    import time

    def count():
        print("One")
        time.sleep(1)
        print("Two")

    def main():
        for _ in range(3):
            count()

    if __name__ == "__main__":
        s = time.perf_counter()
        main()
        elapsed = time.perf_counter() - s
        print(f"{__file__} executed in {elapsed:0.2f} seconds.")
    ```


The output looks like the following. Notice the time difference between the two routines.

=== "Asynchronous"

    ``` bash
    $ python count_async.py
    One
    One
    One
    Two
    Two
    Two
    count_async.py executed in 1.00 seconds.
    ```

=== "Synchronous"

    ``` bash
    $ python count_sync.py
    One
    Two
    One
    Two
    One
    Two
    count_sync.py executed in 3.00 seconds.
    ```

The asynchronous routine will yield back to the main event loop as it `await`s the return value from the subroutine `sleep`. This will allow the other `count()` tasks to start running concurrently. In the traditional synchronous case, the main event loop is halted by `sleep` and subsequently the completion of `count()` before it can start the next task.