---
title: "Idiosyncrasies of AsyncIO"
tags: 
  - asyncio 
categories:
  - python   
last_modified_at: 2020-11-13
---

Here we take a look at the idiosyncrasies and inherent nature of [`asyncio`](https://docs.python.org/3/library/asyncio.html) library in Python. Asyncio is a library to write coroutines which run asynchronously on a single thread, using an underlying event loop. This is different from multi-threaded programing paradigm. `asyncio`-based concurrent coding is similar to web programming with JavaScript or GUI programming.

We will consider three examples. The first example mimics a key portion of code from the open-sourced [SMARTS](https://github.com/huawei-noah/SMARTS) repository. The second and third examples are standalone examples.

Run and consider the output of the first [example](https://gist.github.com/Adaickalavan/0bb9145d294f9b5e497778d2718dbe8f#file-asyncio_smarts-py) below. 

```python
import asyncio
import time

class RemoteAgent:
    def __init__(self):
        pass

    async def _act(self,x,y):
        time.sleep(3)
        return x*y

    def act(self, x, y):
        print("Action(", x, "multiply", y, ") started")
        task = asyncio.create_task(self._act(x,y))
        return task 

class AgentManager:
    def __init__(self):
        self.remoteAgent = RemoteAgent()
        self.task = None

    async def fetch_action(self):
        if self.task == None:
            print("Fetched first action = None")
            return None
        res = await self.task
        print(f"Fetched action = {res}")
        return res

    def send_obs(self,x,y):
        self.task = self.remoteAgent.act(x,y)
        print(f"Task type = {type(self.task)}")

class Smarts:
    def __init__(self):
        self.loop = asyncio.get_event_loop()
        self.agentManager = AgentManager()
    
    def __del__(self):
        self.loop.close()

    def start(self):
        start_time = time.perf_counter()
        iter_time = start_time

        for ii in [1,2,3]:
            action = self.loop.run_until_complete(self.step(ii,ii+2))
            print(f"Iteration {ii} fetched action = {action}")
            cur_time = time.perf_counter()
            elapsed = cur_time - iter_time
            print(f"Iteration {ii} executed in {elapsed:0.2f} seconds.")
            iter_time = cur_time
            print("----------")

        elapsed = time.perf_counter() - start_time
        print(f"Program executed in {elapsed:0.2f} seconds.")

    async def step(self,x,y):
        #Receive
        start_time = time.perf_counter()
        action = await self.agentManager.fetch_action()
        elapsed = time.perf_counter() - start_time
        print(f"Fetch operation took {elapsed:0.2f} seconds.")
        
        #Send
        start_time = time.perf_counter()
        self.agentManager.send_obs(x,y)
        elapsed = time.perf_counter() - start_time
        print(f"Send operation took {elapsed:0.2f} seconds.")
    
        return action

def main():
    smart = Smarts()
    smart.start()

if __name__ == "__main__":
    print("-------------------------")
    print("First analysis of asyncio")
    print("-------------------------")
    main()    
```
The output is:

```bash
-------------------------
First analysis of asyncio
-------------------------
Fetched first action = None
Fetch operation took 0.00 seconds.
Action( 1 multiply 3 ) started
Task type = <class '_asyncio.Task'>
Send operation took 0.00 seconds.
Iteration 1 fetched action = None
Iteration 1 executed in 3.00 seconds.
----------
Fetched action = 3
Fetch operation took 0.00 seconds.
Action( 2 multiply 4 ) started
Task type = <class '_asyncio.Task'>
Send operation took 0.00 seconds.
Iteration 2 fetched action = 3
Iteration 2 executed in 3.00 seconds.
----------
Fetched action = 8
Fetch operation took 0.00 seconds.
Action( 3 multiply 5 ) started
Task type = <class '_asyncio.Task'>
Send operation took 0.00 seconds.
Iteration 3 fetched action = 8
Iteration 3 executed in 3.00 seconds.
----------
Program executed in 9.01 seconds.
```

`loop.run_until_complete()` command ensures all scheduled tasks are completed prior to returning. The `send_obs(x,y)` function schedules a task `task = asyncio.create_task(self._act(x,y))`, but does not run it immediately. The task will only run when the main thread `await`s. Since there is no `await` in the program after the call to `send_obs(x,y)` function, the tasks are run to completion just prior to the function `step(x,y)` returning.

The line `action = await self.agentManager.fetch_action()` in subsequent iterations does not actually need to wait as all the tasks are already completed when the previous iteration of the function `step(x,y)` returned. Hence, the use of `await` here is superficial. 

Run and consider the output of the second [example](https://gist.github.com/Adaickalavan/0bb9145d294f9b5e497778d2718dbe8f#file-asyncio_create_task-py) below.

```python
import asyncio
import time

async def msg(text):
    print("Inside msg")
    await asyncio.sleep(1)
    print(text)

async def long_operation():
    print("Long_operation started")
    await asyncio.sleep(4)
    print("Long_operation finished")

async def run_operations():
    await msg("First")

    # long_operation should be started, but msg should be printed immediately.
    # Create a task to do so.
    start_time = time.perf_counter()
    task = asyncio.create_task(long_operation())
    print("After scheduling task")
    await msg("Second")

    # Await task to finish:
    await task
    elapsed = time.perf_counter() - start_time
    print(f"Time to run scheduled task and second msg = {elapsed:0.2f} seconds.")

def main():
    asyncio.run(run_operations())

if __name__ == "__main__":
    print("-----------------------")
    print("Second asyncio analysis")
    print("-----------------------")
    main()
```
The output is:

```bash
-----------------------
Second asyncio analysis
-----------------------
Inside msg
First
After scheduling task
Inside msg
Long_operation started
Second
Long_operation finished
Time to run scheduled task and second msg = 4.00 seconds.
```

`task = asyncio.create_task(long_operation())` creates an asynchronous task and schedules it to run at the earliest opportunity when the main thread `await`s or idles. The main thread idles upon reaching `await asyncio.sleep(1)` inside `msg(text)` function. The `long_operation()` task starts to run but reaches `await asyncio.sleep(4)` command and yields control to allow `msg(text)` function to complete. Eventually, the `await task` line ensures the `long_operation()` task completes.

Run and consider the output of the third [example](https://gist.github.com/Adaickalavan/0bb9145d294f9b5e497778d2718dbe8f#file-asyncio_subprocess-py) below.

```python
import asyncio
import sys
import time

class RemoteAgent:
    def __init__(self, wait):
        self.subproc = None
        self.wait = wait

    async def connect(self):
        code = """import time
print("Started long process")
time.sleep(10)
print("Completed long process")
"""

        self.subproc = await asyncio.create_subprocess_exec(
            sys.executable, '-c', code,
            stdout=asyncio.subprocess.PIPE,
            stderr=asyncio.subprocess.PIPE)

        print(f"Started subprocess")

        # Read subproc output.
        data = await self.subproc.stdout.readline()
        line = data.decode('ascii').rstrip()
        print(f"Subproc output: {line}")

        if self.wait==True:        
            # Wait for the subprocess to exit.
            await self.subproc.wait()
            # Read subproc output.
            data = await self.subproc.stdout.readline()
            line = data.decode('ascii').rstrip()
            print(f"Subproc output: {line}")
        else:    
            # Check wether the subprocess is alive.
            self.check("First")

        return self.subproc

    def check(self, ref):
        alive = self.subproc.returncode
        if alive == None:
            print(f"{ref}. Proc is alive. Value={alive}")
        else:
            print(f"{ref}. Proc is dead. Value={alive}")

def main(wait):
    ra = RemoteAgent(wait)
    # Create and run subprocess
    subproc = asyncio.run(ra.connect())
    # Check whether subproc is alive using RemoteAgent object
    ra.check("Second")
    # Check whether subproc is alive using subproc handle
    alive = subproc.returncode
    if alive == None:
        print(f"Third. Proc is alive. Value={alive}")
    else:
        print(f"Third. Proc is dead. Value={alive}")
    # End    
    print("End")

if __name__ == "__main__":
    print("-----------------------------------------------")
    print("Run asyncio.subprocess and await its exit")
    print("-----------------------------------------------")
    main(True)
    print("\n")
    print("-----------------------------------------------")
    print("Run asyncio.subprocess but don't await its exit")
    print("-----------------------------------------------")
    main(False)
```
The output is:

```bash
----------------------------------------------
Run asyncio.subprocess and await its exit
----------------------------------------------
Started subprocess
Subproc output: Started long process
Subproc output: Completed long process
Second. Proc is dead. Value=0
Third. Proc is dead. Value=0
End

----------------------------------------------
Run asyncio.subprocess but dont await its exit
----------------------------------------------
Started subprocess
Subproc output: Started long process
First. Proc is alive. Value=None
/usr/lib/python3.7/asyncio/unix_events.py:878: RuntimeWarning: A loop is being detached from a child watcher with pending handlers
  RuntimeWarning)
Second. Proc is alive. Value=None
Third. Proc is alive. Value=None
End
```

In this example, we try to answer the key question of whether an `asyncio.subprocess` can continue running even after a `asyncio.run()` call returns.


In summary, the subprocess does not extend beyond a call to asyncio.run call?