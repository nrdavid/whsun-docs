If you don't have the luxury of running your long programs on supercomputers with access to workload managers like [Slurm](https://slurm.schedmd.com/documentation.html) and you don't have superuser (`sudo`) access, then this tutorial is for you.

## Overview

In this tutorial, you will learn how to have a remote server send you an email when your code terminates by just using `curl`.

## GMail App Passwords

Since this tool will function as an applicaton, we first need to create a secure key code to allow our GMail account to automatically send emails. Visit [App passwords](https://myaccount.google.com/apppasswords), log into your account and create a new App password. Record this key value for later.

## Inserting into your bash script

Using your GMail app, we can insert the curl command below at the end of any batch script to send us an email when our script terminates.

``` bash
#!/bin/bash

# Some script that runs for a long time
python foo.py

# Wait for all background processes to finish
wait

# Send email 
curl --url 'smtps://smtp.gmail.com:465' --ssl-reqd \
  --mail-from 'sender@gmail.com' \ # (1)!
  --mail-rcpt 'recipient@umich.edu' \ # (2)!
  --user 'sender@gmail.com:YOUR_APP_PASSWORD' \ # (3)!
  -T <(echo -e 'From: sender@gmail.com\nTo: recipient@umich.edu\nSubject: Server Automatic\n\nCode done!') # (3)!
```

1.  The email used to create the app password (i.e. the sender)
2.  The recepient (can be anyone).
3.  Place your app password after the colon
4.  Subject: Server Automatic. Body: Code done!

???+ tip

    Remember that in order to execute a bash script you must first add execute permissions to bash script.
    ```bash
    chmod +x <filename>
    ```
    To run the script as a background process, you can insert the `&` after the execution.
    ```bash
    ./filename &
    ```