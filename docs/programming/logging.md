The [logging](https://docs.python.org/3/library/logging.html) library is part of The Python Standard Library and allows users to implement a flexible event logging system for applications and libraries. Logging is typically deployed for more complex applications that run for extended periods time. Logging allows the programmer to monitor the application during runtime and afterwards for post-mortem analysis. Credits to [realpython](https://realpython.com/python-logging/) for inspiration in some of these examples on how to use this library.

## Basic usage

To start using the logger, simply import logging and begin inserting logging messages into your code as such.

``` py
>>> import logging
>>> logging.warning("Remain calm!")
WARNING:root:Remain calm!
```

How does this differ from just using print statements? In its most basic form it does not. However, in more complex programs logging provides additional functionality that simplifies event logging. 

## Logging levels

| Log Level  | Function              | Description                                                                       |
|------------|-----------------------|-----------------------------------------------------------------------------------|
| DEBUG      | `logging.debug()`      | Provides detailed information that’s valuable to you as a developer.              |
| INFO       | `logging.info()`       | Provides general information about what’s going on with your program.             |
| WARNING    | `logging.warning()`    | Indicates that there’s something you should look into.                            |
| ERROR      | `logging.error()`      | Alerts you to an unexpected problem that’s occurred in your program.              |
| CRITICAL   | `logging.critical()`   | Tells you that a serious error has occurred and may have crashed your app.        |

The logger can be configured to different levels to choose which level of log messages you want to record. For example, setting the logging level to WARNING (the default logging level) will result in WARNING and all higher level log messages to be recorded.

``` py
>>> logging.debug("This is a debug message")

>>> logging.info("This is an info message")

>>> logging.warning("This is a warning message")
WARNING:root:This is a warning message

>>> logging.error("This is an error message")
ERROR:root:This is an error message

>>> logging.critical("This is a critical message")
CRITICAL:root:This is a critical message
```

## Logging configuration

The logger can be configured using the `logging.basicConfig` method. See the example below.

``` py
# foo.py

from datetime import datetime
import logging

def main():
    logging.info("I'm doing some stuff")
    logging.error("Oh no! Something went wrong.")

if __name__ == "__main__":
    current_datetime = datetime.now().strftime("%Y-%m-%d_%H-%M-%S") # (1)
    logging.basicConfig(filename=f"logs/foo_{current_datetime}.log", # (2)!
                    filemode='w', # (3)!
                    level=logging.DEBUG, # (4)!
                    format='%(asctime)s - %(levelname)s - %(message)s') # (5)!
    logging.info("START PROGRAM")
    main()
    logging.info("END PROGRAM")
```

1.  Get the current date and time
2.  Configure the logger using `logging.basicConfig`. We can specify the `filename` to save the output of the logger to.
3.  Set the `filemode` to write.
4.  Set the logging level to DEBUG.
5.  Format the logger to include the time, level name, and then write the message.

This will generate the output seen below. Depending on your editor, the messages will also be colored.

``` log
2024-09-19 15:18:27,397 - INFO - START PROGRAM
2024-09-19 15:18:27,397 - INFO - I'm doing some stuff
2024-09-19 15:18:27,397 - ERROR - Oh no! Something went wrong.
2024-09-19 15:18:27,397 - INFO - END PROGRAM
```

???+ tip "Tip - Instantiating your own logger"

    Different modules in your application can have their own loggers with different configurations. Below is an example of instantiating a logger class. Using this you can also see which module different logs are coming.

    ``` py
    >>> import logging
    >>> logger = logging.getLogger(__name__) # (1)!
    >>> logger.warning("Look at my logger!")
    Look at my logger!
    ```

    1.  This will be the name of the module from which your code is in. Likely **"\_\_main\_\_"**, at least in this case.