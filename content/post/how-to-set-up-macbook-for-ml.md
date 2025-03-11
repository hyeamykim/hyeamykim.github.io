---
title: "How to Set Up Macbook for Ml"
date: 2025-03-06T11:29:24+01:00
Description: ""
Tags: ["how-to", "mac OS", "terminal", "python"]
Categories: []
DisableComments: false
---

## Introduction

It was my first time setting up and using MacBook (MacBook Air M1, Mac OS Sequoia 15.3) to use it for data analytics, ML, and LLM projects. Though I found helpful YouTube tutorials, it was still quite a process, so I thought I would share it in case anyone needs it as well.

I followed these YouTube tutorials by Danil Zherebtsov:  
(You can watch them in order if it's easier for you to process information by watching instead of reading.)

- [Mac Terminal Setup in YouTube](https://www.youtube.com/watch?v=yVZg547GiYg): tells you how to set up terminal on Mac, using iTerm instead of default Mac terminal for more personalized settings
- [Mac Python Setup in YouTube](https://www.youtube.com/watch?v=u62MifBPI5g): tells you how to install Python on Mac using pyenv and pyenv-virtualenv for managing different Python versions and environments
- [Mac Python Setup for Data Science and ML YouTube](https://www.youtube.com/watch?v=QcMAHnHlIKk): tells you how to set up data science and ML project environment using VS Code and pyenv

In this blog post, I will explain the MacBook set up process that I followed step by step. 

## Outline

Simply put, you need three things for ML projects on your MacBook. I will briefly explain what those are in this section and how to install them in the next. 

- IDE: An integrated development environment (IDE) is a software application that helps programmers develop software code efficiently. It enables software editing, building, testing, and packaging. The examples include Eclipse, IntelliJ, Visual Studio, PyCharm, and cursor. For Python programming, Visual Studio Code and PyCharm are popular choices. 
- Python environment: Python, it goes without saying, is one of the most widely used programming language for data analytics and ML. Though MacBook comes with default Python installation, you can easily manage different projects, which might require different versions of Python and/or different types of Python libraries, with pyenv and pyenv-virtualenv, which lets you install different Python versions and create/manage virtual environments. 
- Python libraries for data science and machine learning: Python libraries such as Pandas, Numpy, Scikit-learn, Matplotlib, PyTorch and Tensorflow facilitates importing and exporting data, calculations on large arrays and matrices, preparing data for ML algorithms, and applying ML algorithms. Once you have Python installed, you can easily download the libraries you need with pip or conda commands. 

## IDE: VSCode

I've been using VS Code, so that's what I downloaded for MacBook, but you can choose on your own. 
If VS Code is your choice, you can download [VS Code](https://code.visualstudio.com/download).
Once it's installed, you can get Extensions for Python, Jupyternotebook, CoPilot, etc.

## Python: pyenv, pyenv-virtualenv

To download Python with pyenv on Mac, you first need Xcode and Homebrew and check if you have .zshrc file in your home directory. 

### Xcode

[Xcode](https://developer.apple.com/xcode/) is Apple's tool for developing iOS and Mac apps. You need it to compile and install software packages, which are usually open-source Unix packages that need to be compiled before installation. Since Xcode is a huge app, you can just download Xcode command line tools (which includes compilers) without installing Xcode. 

You can check if you have Xcode installed by typing the following command to the terminal.
```
xcode-select -p
```

You can install it by typing the following command to the terminal.
```
xcode-select --install
```

### Homebrew

[Homebrew](https://brew.sh/) is a Mac software package manager that lets you install software for the command line.  

Before installing it, go to Downloads and make a homebrew folder with the following command in the terminal.
```
cd ~/Downloads
mkdir homebrew
```

Then, install homebrew and move it to opt directory (under home directory).
```
curl -L https://github.com/Homebrew/brew/tarball/master | tar xz --strip 1 -C homebrew
sudo mv homebrew ~/opt/homebrew
```

If you add the path in the current terminal with ```export PATH="/opt/homebrew/bin:$PATH"```,  
you can start using brew command and install packages.
```
brew install wget
brew install git
```
### .zshrc file

.zshrc file and .zprofile are the two zsh shell (which Mac uses by default) initialization files that are commonly used for configuration:
- ~/.zshrc (Zsh Run Control): This file is best for adjusting the appearance and behavior of the shell, such as setting command aliases and adjusting the shell prompt.
- ~/.zprofile (Zsh Profile): This file is ideal for commands that should be executed only when the terminal window is opened, such as setting the $PATH environment variable.

You can read more about the differences and mac OS configuration [here](https://mac.install.guide/terminal/zshrc-zprofile).

But for the sake of this blog post, I'm not differentiating between the two and configuring the shell with .zshrc file only. 

Check for .zshrc file in your home directory. You can display hidden files with cmd+shift+. command. 
If you don't have .zshrc file in your home directory, create it with touch command in terminal.
```
touch ~/.zshrc
```

In the .zshrc file, you can add the following line to be able to use brew command from the terminal. 
```
export PATH="/opt/homebrew/bin:$PATH"
```

### Python

#### Check for existing Python versions 
Check if you have Python installed and where it is located. 
If you had Python installed before, it is easier to delete it before using Python with pyenv. If you have Python installed in any other way, pyenv will update your path automatically as you switch Python versions anyways. 
So, for example, if you have Python in ```/Library/Frameworks/Python.framework/Versions/3.8/bin/python3```, uninstall and remove it from your ```$PATH```.

From the terminal, to delete Python in full:
```
sudo rm -rf /Library/Frameworks/Python.framework 
```
To delete a certain version of Python:
```
sudo rm -rf /Library/Frameworks/Python.framework/Versions/[version number]
```
#### Install pyenv and pyenv-virtualenv
Then you can install pyenv and pyenv-virtualenv with Homebrew with the following command.
```
brew install pyenv 
brew intsll pyenv-virtualenv
```
You also need to install the following pyenv dependencies.
```
brew install openssl readline sqlite3 xz zlib tcl-tk libffi
```

To properly initialize pyenv and pyenv-virtualenv, you can paste the following to .zshrc file. 
```
export PYENV_ROOT="$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
export CONFIGURE_OPTS="--with-openssl=$(brew --prefix openssl)"
export PATH="$PYENV_ROOT/shims:$PATH"
export PATH="$PYENV_ROOT/bin:$PATH"
```

With pyenv,  

you can check which Python versions you have: ```pyenv versions```  
install Python versions you want: ```pyenv install 3.12```  
specify a deafult Python version you want: ```pyenv global 3.12```  
create a virtual environment: ```pyenv virtualenv 3.12 myproject```  
activate a virtual environment from the directory of your choice: ```pyenv local myproject```  

### Miniforge

When you install Python with pyenv, it can be tricky to install Python libraries that can be installed with conda.
So to be able to seemlessly install Python packages with conda without installing Anaconda, you can install miniforge with pyenv. Miniforge provides installers for the commands conda and mamba, so that you can use conda commands in terminal. 

You can check Python versions that can be installed with pyenv with:
```
pyenv install --list
```

The first miniforge version comes with Python 3.10 and the second one comes with Python 3.12.
You can select any miniforge version you want and install it.
```
pyenv install miniforge3-4.14.0-1
pyenv install miniforge3-24.11.3-0
```

## Python libraries: 

Within the selected Python environment, you can install any packages you need with pip or conda command:
```
pip install tensorflow-metal
conda install lightgbm torch torchvision torchaudio
```

If you have a pip_requirement.txt file (like in the YouTube tutorial) that lists all Python libraries you need for a project, you can install it at once:
```
pip install -r pip_requirements.txt
```

## References
- Mac Terminal Setup in YouTube: https://www.youtube.com/watch?v=yVZg547GiYg
- Mac Terminal Setup in GitHub: https://github.com/DanilZherebtsov/ultimate_mac_terminal_setup
- Mac Python Setup in YouTube: https://www.youtube.com/watch?v=u62MifBPI5g
- Mac Python Setup for Data Science and ML YouTube: https://www.youtube.com/watch?v=QcMAHnHlIKk
- VS Code: https://code.visualstudio.com/
- Xcode: https://developer.apple.com/xcode/
- Homebrew: https://brew.sh/
- Mac OS Configuration: https://mac.install.guide/terminal/zshrc-zprofile
- pyenv-virtualenv: https://github.com/pyenv/pyenv-virtualenv
- miniforge: https://github.com/conda-forge/miniforge



