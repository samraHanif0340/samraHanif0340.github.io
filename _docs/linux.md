---
title: "Linux"
---

## Proxy
1. Need to set proxy for Linux `apt` if we use `apt` behind a corporate proxy, e.g., `http://10.0.0.0:8080/`. Hence, create a `apt.conf` file at `/etc/apt/` such that `/etc/apt/apt.conf` file contains the following.
    ```
    Acquire::http::proxy "http://10.0.0.0:8080/";
    Acquire::https::proxy "https://10.0.0.0:8080/";
    ```
1. Restart the networking to start using the new proxy setting.
    ``` bash
    $ systemctl restart networking
    ```

## tmux
1. `tmux` enables us to run tasks persistently on servers. We can safely disconnect from the server without disrupting the tasks and reconnect at a later time to rejoin the running tasks.
1. Following are a list of important `tmux` commands. 
    ```bash
    # Install tmux
    $ sudo apt-get install tmux 

    # New session with name 'xyz'
    $ tmux new -s xyz

    # Scroll within a tmux session
    - Ctrl-b [ 
    - Then use normal navigation keys to scroll around (e.g., Up Arrow or PgDn). 
    - Press q to quit scroll mode.

    # Detach from current session
    - Ctrl+b d 

    # Reconnect to a session named 'xyz'
    $ tmux attach-session -t xyz

    # Show running sessions from command prompt
    $ tmux ls

    # Show all sessions from within a session
    - Ctrl+b s

    # Navigate between sessions from within a session
    - Ctrl+b (  # previous session
    - Ctrl+b )  # next session

    # Kill a named session from within or outside a session
    $ tmux kill-session -t xyz

    # Kill all sessions
    $ tmux kill-server
    ```

## Find and kill processes
1. Assume that we would like to find and kill all running `python3.7 client.py` processes. 
1. Find PID of all `python3.7 client.py` proceses 
    ```bash
    $ ps -ef | grep 'python3.7 client.py'
    ```
1. Kill process 
    ```bash
    # Kill process using PID
    $ kill -9 PID
    # Kill process using partial name matches
    $ pkill -9 -f python3.7
    ```

## SSH to remote Ubuntu
1. Install SSH in remote computer and open SSH port through the firewall in remote computer. Exceute these commands in remote computer.
    ```bash
    $ sudo apt update
    $ sudo apt install openssh-server
    $ sudo ufw allow ssh
    ```
1. Generate SSH public/private keys in client computer and send it to remote computer. Execute these commands in client computer.
    ```bash
    $ ssh-keygen -t rsa -b 2048
    $ cat ~/.ssh/id_rsa.pub | ssh user@198.51.100.0 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys"
    ```
    Here, user@198.51.100.0 represents username@remote_ip_address.

## SSH with password and username
1. Send password and username in a single command, for instance, while transferring `file.tar` to remote a computer at address `10.193.100.100`. 
    ```bash
    $ sudo apt-get install sshpass
    $ sshpass -p <password> scp ./file.tar <username>@10.193.100.100:<dest folder> 
    ```

## Installing packages
1. If you encounter broken packages error, such as,
    ```bash
    $ sudo apt install libgpgme11-dev
    Some packages could not be installed. This may mean that you have requested an impossible situation or if you are using the unstable distribution that some required packages have not yet been created or been moved out of Incoming.

    The following information may help resolve the situation:

    The following packages have unmet dependencies:
    libgpgme11-dev : Depends: libgpgme11 (= 1.6.0-1) but 1.8.0-3ubuntu2~ubuntu16.04~ppa1 is to be installed
    E: Unable to correct problems, you have held broken packages.
    ```
    Consider installing using the following command for more information on the package dependency problem.
    ```bash
    $ sudo apt install libgpgme11-dev -o Debug::pkgProblemResolver=true -o Debug::Acquire::http=true
    Starting pkgProblemResolver with broken count: 1
    Starting 2 pkgProblemResolver with broken count: 1
    Investigating (0) libgpgme11-dev [ amd64 ] < none -> 1.6.0-1 > ( libdevel )
    Broken libgpgme11-dev:amd64 Depends on libassuan-dev [ amd64 ] < none -> 2.4.2-2 > ( libdevel )
    Considering libassuan-dev:amd64 1 as a solution to libgpgme11-dev:amd64 9999
    Re-Instated libassuan-dev:amd64
    Broken libgpgme11-dev:amd64 Depends on libgpg-error-dev [ amd64 ] < none -> 1.21-2ubuntu1 > ( libdevel )
    Considering libgpg-error-dev:amd64 1 as a solution to libgpgme11-dev:amd64 9999
    Re-Instated libgpg-error-dev:amd64
    Broken libgpgme11-dev:amd64 Depends on libgpgme11 [ amd64 ] < 1.8.0-3ubuntu2~ubuntu16.04~ppa1 > ( libs ) (= 1.6.0-1)
    Considering libgpgme11:amd64 4 as a solution to libgpgme11-dev:amd64 9999
    Done
    Some packages could not be installed. This may mean that you have
    requested an impossible situation or if you are using the unstable
    distribution that some required packages have not yet been created
    or been moved out of Incoming.
    The following information may help resolve the situation:

    The following packages have unmet dependencies:
    libgpgme11-dev : Depends: libgpgme11 (= 1.6.0-1) but 1.8.0-3ubuntu2~ubuntu16.04~ppa1 is to be installed
    E: Unable to correct problems, you have held broken packages.
    ```