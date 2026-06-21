---
title: "常用软件环境"
toc: true
toc_label: "文章目录"
toc_icon: "list-ul"
lang: zh-CN
categories:
  - Blog
tags:
  - module
  - Python
  - MPI
  - 编译器
---

## 1. 系统环境公开说明

服务器提供 Linux 科研计算环境和常见科学计算软件支持。页面不展示具体操作系统版本、内核版本、编译器版本、MPI 版本、模块路径和系统级安装路径。

组内用户应以登录后的实际命令输出和内部软件环境说明为准。

## 2. module 使用

如果服务器配置了 Environment Modules 或 Lmod，可使用以下命令查看软件环境。

查看可用软件模块：

```bash
module avail
```

清空当前环境：

```bash
module purge
```

加载软件模块：

```bash
module load <module_name>
```

查看已加载模块：

```bash
module list
```

如果 `module` 命令不可用，说明当前系统可能尚未配置模块系统，请使用系统自带软件、个人 Conda 环境，或联系管理员安装公共软件环境。

## 3. 建议维护的软件类型

正式投入使用后，建议逐步整理并固定以下软件环境：

| 软件类型 | 示例 | 用途 | 建议说明 |
|---|---|---|---|
| 编译器 | GCC / Clang / oneAPI 等 | C/C++/Fortran 编译 | 记录加载方式和适用范围 |
| MPI | OpenMPI / MPICH / Intel MPI 等 | 并行计算 | 记录 `srun` / `mpirun` 推荐用法 |
| Python | Miniconda / Anaconda | Python 科研环境 | 建议用户使用个人环境 |
| 数学库 | BLAS / LAPACK / FFTW 等 | 高性能数学计算 | 按程序需求选择 |
| HDF5 | HDF5 / Parallel HDF5 | 科学数据格式 | 并行 I/O 需单独说明 |
| 量子化学 | PySCF / QE / 其他组内软件 | 电子结构计算 | 根据课题组实际需求安装 |
| QMC/AFQMC | 组内科研程序 | 量子蒙特卡洛计算 | 建议记录编译选项和依赖 |

## 4. Conda 环境建议

创建新环境：

```bash
conda create -n myenv python=3.10
```

激活环境：

```bash
conda activate myenv
```

安装常用包：

```bash
pip install numpy scipy matplotlib pandas
```

建议每个项目使用独立环境，避免不同项目之间依赖冲突。

## 5. 编译程序前建议记录

```bash
which gcc
which g++
which gfortran
which mpirun
which srun
gcc --version
mpirun --version 2>/dev/null || true
module list 2>&1 || true
```

这些信息有助于复现实验，也有助于排查编译和运行错误。公开交流时请注意删除内部路径和具体主机信息。

## 6. 软件安装建议

普通用户建议优先使用：

- 个人 Conda 环境；
- 用户目录下源码编译安装；
- 项目目录中的虚拟环境；
- 管理员维护的公共 module。

不要随意修改系统级软件、公共库或他人环境。需要系统权限安装的软件，应联系管理员统一维护。
