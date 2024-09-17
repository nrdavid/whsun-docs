# 0. Overview

This tutorial will get you started with basic python programming using conda for environment management and Visual Studio Code as an integrated development environment (IDE).

# 1. Installing Conda

[Conda](https://conda.io/projects/conda/en/latest/index.html) is an environment manager frequently used with Python. There are a few different installers, but we'll specifically be looking at [Miniconda](https://docs.anaconda.com/miniconda/), a free minimal installer for conda.

## 1.1 Windows Subsystem for Linux (WSL)

If you're developing on Windows, it is recommended that you first install Windows Subsystem for Linux (WSL) on your machine. WSL is a virtualization software that lets developers install a Linux distribution (such as Ubuntu, OpenSUSE, Kali, Debian, Arch Linux, etc) and use Linux applications, utilities, and Bash command-line tools directly on Windows, unmodified, without the overhead of a traditional virtual machine or dualboot setup.

To setup, follow the instructions below:

1. Open PowerShell in **administrator** mode by right-clicking and selection "Run as administrator".
2. Install wsl with the default Ubuntu distribution.
``` powershell
wsl --install
```