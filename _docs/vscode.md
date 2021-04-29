---
title: "Integrated Development Environment"
---

## IDE
VSCode is suggested as the standard IDE. VSCode is open source and free. It can be downloaded from its official [website](https://code.visualstudio.com/).

The following are the recommended extensions to be installed in the IDE, which facilitate code development.
+ autoDocstring - Nils Werner
+ C/C++ - Microsoft
+ CMake - twxs
+ CMake Tools - Microsoft
+ Docker - Microsoft
+ Excel Viewer - GrapeCity
+ GitLens Git supercharged - Eric Amodio
+ Go - Microsoft
+ GoogleTest Adapter
+ Kubernetes - Microsoft
+ OpenCL
+ Python - Microsoft
+ vscode-pdf
+ vscode-proto3 - zxh404
+ YAML - Red Hat

## Debugging
+ Create a `launch.json` file and place it in `~/.vscode` folder.
+ A sample `launch.json` file configuration for debugging a python program in virtual environment is shown below.

    ```json
    {
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "xingtian",
                "type": "python",
                "request": "launch",
                "cwd": "${workspaceFolder}/workspaces/xingtian",
                "python": "${workspaceFolder}/workspaces/xingtian/.venv/bin/python3.7",
                "program": "${workspaceFolder}/workspaces/xingtian/xt/main.py",
                "args": [
                    "-f", 
                    "./examples/smarts.yaml",
                ],
                "console": "integratedTerminal",
            }
    ```