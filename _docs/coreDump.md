---
title: "Core Dump"
---

## Enable core dump
1. Ensure that they are enabled. Run the following command. If the reply is not `unlimited`, the core dumps are disabled or limited.
    ```bash
    $ ulimit -c
    ```
1. Run `ulimit -c unlimited` to enable creation of core dumps from the current shell session.
    ```bash
    $ ulimit -c unlimited
    ```

## File location    
1. Location of core dump config file
    ```bash
    $ cat /proc/sys/kernel/core_pattern
    |/usr/share/apport/apport %p %s %c %d %P %E
    ```
1. Rewrite contents of `core_pattern` file so that the core is dumped to the current working directory.
    ```bash
    $ sudo bash -c 'echo core.%e.%p.%t > /proc/sys/kernel/core_pattern'
    ```

## Usage    
1. Simple usage of GDB, to debug coredump files
    ```bash
    $ gdb <executable_path> <coredump_file_path>
    ```
1. After getting inside the GDB prompt (on execution of the above command), type
    ```bash
    $ (gdb) where
    $ (gdb) bt
    $ (gdb) thread apply all bt
    ```
    This will get the information, of the stack, where we can analayze the cause of the crash/fault.