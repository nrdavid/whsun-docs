## Overview

In this tutorial we will cover concurrency and (embarassingly) parallel programs. We will provide a high-level overview of each and some basic example scripts involving each.

## Asyncio, threading, and multiprocessing

In another tutorial we covered [asyncio](asynchrony.md) in Python; Here, we will cover the threading and multiprocessing libraries in Python. To the naive programmer, all of these libraries seem pretty similar and rightfully so. Below is table that describes when we should use each library.

| Library        | Use Case                                       | Best For                                     | Example Scenarios                                  |
|----------------|------------------------------------------------|----------------------------------------------|---------------------------------------------------|
| `asyncio`      | Asynchronous I/O operations, many waiting operations                    | I/O-bound tasks, cooperative multitasking    | Networking, web scraping, file I/O                |
| `threading`    | Concurrent tasks with shared memory            | I/O-bound tasks with parallelism needed      | Web servers, user interface applications          |
| `multiprocessing` | CPU-bound tasks with no shared memory       | CPU intensive tasks            | Data processing, machine learning, image processing|


## Concurrency

**Concurrency** is the ability for tasks in program or algorithm to be executed during overlapping time periods rather than purely sequentially. In these programs, the CPU core switches between "tasks" (i.e. threads) without necessarily completing each one after switching. This enables programs to execute more efficiently even on single-threaded, single core CPUs.

The Python Standard Library provides a module named `threading` that allows us to build programs with concurrency. Let's analyze the following program.

=== "Code"
    ``` py
    import threading, time

    def foo(snooze: float):
        print('start foo')
        time.sleep(snooze) # (1)!
        print('end foo')

    def bar(snooze: float):
        print('start bar')
        time.sleep(snooze)
        print('end bar')

    def main():
        x = threading.Thread(target=foo, args=(1,)) # (2)!
        y = threading.Thread(target=bar, args=(2,))
        x.start() # (3)!
        y.start()

    if __name__ == "__main__":
        start_time = time.time()
        main()
        finish_time = time.time()
        print(f"Finished in: {finish_time - start_time:.2f} seconds")
    ```

    1. `sleep` used to simulate some operation.
    2. `target` specifies the function and `args` is a tuple with the function arguments.
    3. `start` used to start a thread.

=== "Output"
    
    ``` bash
    $ python test.py
    start foo
    start bar
    Finished in: 0.00 seconds
    end foo
    end bar
    ```

Finished in 0.00 seconds? That was quick! Did the program really terminate here? *No*. Since both functions were halted with the sleep function, the CPU decided to keep working on the main thread. Then when each function was done, `foo` then `bar` it terminated the program.

If we want to halt the main thread and add some level of synchronization, we can wait for the threads to finish first with the `join()` function.

=== "Code"

    ``` py
    import threading, time

    def foo(snooze: float):
        print('start foo')
        time.sleep(snooze)
        print('end foo')

    def bar(snooze: float):
        print('start bar')
        time.sleep(snooze)
        print('end bar')

    def main():
        x = threading.Thread(target=foo, args=(1,))
        y = threading.Thread(target=bar, args=(2,))
        x.start()
        y.start()
        x.join() # (1)!
        y.join()
    if __name__ == "__main__":
        start_time = time.time()
        main()
        finish_time = time.time()
        print(f"Finished in: {finish_time - start_time:.2f} seconds")
    ```

    1. Notice the `join()` function calls added here.

=== "Output"

    ``` bash
    $ python test.py
    start foo
    start bar
    end foo
    end bar
    Finished in: 2.00 seconds
    ```

Now our code finishes in 2.00 seconds as expected. If we make `y` the slowest task by passing a `snooze` value of 0.5, we get the following output.

``` bash
$ python test.py
start foo
start bar
end bar
end foo
Finished in: 1.00 seconds
```

`bar` finishes first (since it slept for less), but we still halt the main thread until both have finished.

???+ tip "Tip - Lock"

    You can use `threading.Lock` to introduce synchronization into your code by only allowing threads with the [Lock](https://docs.python.org/3/library/threading.html#lock-objects) to operate and by waiting for the lock's release in order to start other threads.

???+ warning "Warning - Global Interpreter Lock (GIL)"

    Due to the [Global Interpreter Lock](https://docs.python.org/3/glossary.html#term-global-interpreter-lock) (GIL) in CPython, only one thread can execute Python code at a single instance in time. This ensures [thread safety](https://en.wikipedia.org/wiki/Thread_safety#) to prevent multiple threads reading/writing to same piece of memory at the same time -- which can lead to unexpected behavior.

## (Embarrassing) Parallelism

[Multiprocessing](https://docs.python.org/3/library/multiprocessing.html) from the Python Standard Library is similar to multithreading, but doesn't suffer the same limitations from the Python GIL. In `multiprocessing`, we are spawning separate python interpreters each with their own GIL.

Multiprocessing is best used when you have multiple tasks to be completed that involve CPU intensive computations. The python code is nearly identical to that of `threading`.

=== "Code"

    ``` py
    import multiprocessing, time

    def func(snooze: int):
        print("Process id:", multiprocessing.current_process().pid)
        time.sleep(snooze)
        return multiprocessing.current_process().pid

    def main():
        p1 = multiprocessing.Process(target=func, args=(1,)) 
        p2 = multiprocessing.Process(target=func, args=(1,)) 

        p1.start() # (1)!
        p2.start()

        p1.join() # (2)!
        p2.join() 

    if __name__ == "__main__":
        start_time = time.time()
        main()
        end_time = time.time()
        print(f"Finished in: {end_time - start_time:.2f} seconds")
    ```

    1.  Starts the process.
    2.  Waits until the process is done to continue.

=== "Output"

    ``` bash
    $ python foo.py
    Process id: 87111
    Process id: 87112
    Finished in: 1.06 seconds
    ```

Notice how a new PID is given to each process. If we have many tasks, we can use the process `Pool` to automatically start and join processes.

=== "Code"

    ``` py
    import multiprocessing, time

    def func(snooze: int):
        print("Process id:", multiprocessing.current_process().pid)
        time.sleep(snooze)

    def main():
        with multiprocessing.Pool() as p: # (1)!
            p.map(func, [1, 1, 1, 1, 1])

    if __name__ == "__main__":
        start_time = time.time()
        main()
        end_time = time.time()
        print(f"Finished in: {end_time - start_time:.2f} seconds")
    ```

    1.  The `Pool` with no arguments uses the the result of `os.cpu_count()` to determine how many subprocesses can be active at any given time.

=== "Output"

    ``` bash
    $ python foo.py
    Process id: 88179
    Process id: 88181
    Process id: 88180
    Process id: 88185
    Process id: 88187
    Finished in: 1.12 seconds
    ```

???+ tip

    Depending on your machine's configuration and whether you're using `threading` or `multiprocessing` or both, it may be advantageous to chunk the data you're operating on (if each subprocess is independent) and instantiate python PIDs through a bash script. You can use the [argparse](https://docs.python.org/3/library/argparse.html) library to add command line arguments to a script and use a bash script like such to do this.
    ``` bash
    #!/bin/bash

    # Loop through years from 2013 to 2023
    for year in {2023..2023}; do
        # Run the Python script with the year argument in the background
        python extract_batch.py -y $year &
    done
    # Wait for all background processes to finish
    wait

    echo "All extraction processes completed."
    ```