---
title: Conda cheatsheet
description: Conda 简介
categories:
 - Conda
tags:
 - Conda
 - software
---

# Introduction
[Conda](https://conda.io/docs/index.html) 是一个开源的包和环境管理系统，适用于 Python, R, Ruby 等各种语言，能够构建独立的环境用于不同任务  
Anaconda 和 Miniconda 均为 python 的一种发行版本，集成了 conda 以及其他的一些包和软件。[Anaconda](https://www.anaconda.com/)  包含了 conda, conda build, Python 以及超过100种的开源包及依赖项，比较庞大；而 [Miniconda](https://conda.io/miniconda.html) 仅包含 conda，python 以及少量必需的包，其他的包可以后续根据实际需要安装  

基本操作  
```shell
conda --version    # 等价于 conda -V 检查版本号
conda info
conda update conda # 更新
```

# Environment  
Conda `environment` 指的是包含所安装的特定 conda 包的路径，默认的环境名称为 `base`。当你改变某一环境时，其他的环境并不会受到影响。可以通过 `activate` 和 `deactivate` 实现环境的切换。另外还可以通过将 `environment.yaml` 拷贝给他人实现环境的共享    
  
```shell
conda create -n <myenv>    # create an environment called myenv，默认在目录 /env/ 下
conda create -n myenv scipy    # 创建环境时同时安装指定的包。建议一次性安装所有要用的包，避免单个安装发生冲突
conda remove -n <myenv> --all    # remove an environment
source activate <myenv>  
source deactivate              # Change your current environment back to the default (base)
conda info --envs              # 列出所有构建的环境，其中当前所处环境前会表上*号  
conda env list                 # 同上
```
  
# Packages  
```shell
conda install <package>
conda search <package>    # Check to see if a package you have not installed is available from the Anaconda repository
conda list                # 列出环境中所有的包
conda update <package>    # update a package
conda remove <package>    # remove a package
# 以上操作均在当前env中进行 如果想指定某个环境 则加上 -n <env> 即可
```
  
# Configuration  
## .condarc conda configuration file
`.condarc` 文件可以包含多种设置:  
* conda 从哪里搜索包 (Where conda looks for packages)  
* conda 是否以及如何使用代理服务器 (If and how conda uses a proxy server)  
* 创建新环境时同时包含的默认的包或特征 (Default packages or features to include in new environments)  
  
创建或修改 `.condarc` 文件, 可以使用命令 `conda config` 或通过文本编辑器直接操作 `.condarc` 文件，并将其保存至用户主目录（第一次使用 conda config 时会默认创建）；也可将文件放在 conda 软件安装的根目录下，e.g. `~/miniconda3/`（即base环境对应的路径），这样该文件中的设置将会覆盖用户主目录下文件的设置  
  
## channels  
`defaults` 通道包含来自 [repo.continuum.io](https://repo.continuum.io/pkgs/) 的多个通道。其中 main channel 包含了绝大部分新的包，采用新的编译器；free channel 则包含未采用新编译器构建的包，基本上都与 main 中的兼容；此外，还有其他的一些通道包含在内。另外也可以通过在 .condarc 文件中设置 [`default_channels`](https://conda.io/docs/user-guide/configuration/use-condarc.html#default-channels-default-channels) 来覆盖默认的这些设置  
如果仅希望在某个环境中进行通道设置，可以将 `.condarc` 文件放在该环境的 root directory 下, e.g. `~/miniconda3/envs/test/.condarc`，即仅作用于 test 环境；或者在使用 `conda config` 时使用 `--env` 参数  
  
参考配置命令：  
```shell
conda config --add channels conda-forge    # 后写入的通道将会放在最前面
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/bioconda/
conda config --set show_channel_urls yes
```

`.condarc` 文件示范  
```
channels:
  - https://mirrors.ustc.edu.cn/anaconda/cloud/bioconda/
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  - conda-forge
  - defaults
show_channel_urls: True
```
  
## Update conda automatically
当设置为 `auto_update_conda: True` (default)， 每当用户在根环境下更新或者安装某个包，conda 就会自动进行更新。当设置为 `False`时，只有当运行 `conda update` 命令是才会进行更新  
  
## Show channel URLs (`show_channel_urls: True`)  
下载包或者使用 `conda list` 命令时，显示完整的 URL；默认为 `False`  
  
## Add pip as Python dependency (`add_pip_as_python_dependency`)
将 pip, wheel 和 setuptools 作为 Python 的依赖进行安装，确保当 Python 安装时，这三者也同时被安装；默认为 `True`  
  
## Always add packages by default (`create_default_packages`)
当构建新的环境时，默认同时添加特定的包；可以在创建环境时加上标签 `--no-default-packages` 来覆盖该设置。默认为不添加任何包  
```shell
create_default_packages:
  - pip
  - ipython
  - scipy=0.15.0  
```
