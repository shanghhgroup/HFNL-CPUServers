---
title: "Slurm 作业提交说明"
toc: true
toc_label: "文章目录"
toc_icon: "list-ul"
lang: zh-CN
categories:
  - Blog
tags:
  - Slurm
  - sbatch
  - squeue
---

## 1. Slurm 基本说明

服务器使用 Slurm 作业调度系统管理计算任务。用户应通过作业脚本申请 CPU、内存、运行时间和输出日志，而不是在登录环境中直接运行大型计算。

本页面不展示具体 Slurm 版本、控制节点、真实分区名、节点列表、资源上限和内部调度配置。

## 2. 常用命令

| 命令 | 作用 |
|---|---|
| `sinfo` | 查看分区和节点状态 |
| `squeue -u $USER` | 查看自己的任务 |
| `sbatch job.sh` | 提交任务 |
| `scancel JOBID` | 取消任务 |
| `scontrol show job JOBID` | 查看任务详细信息 |
| `sacct -j JOBID` | 查看历史任务记录 |

## 3. 最小测试脚本

文件名：`test_slurm.sh`

```bash
#!/bin/bash
#SBATCH -J test_slurm
#SBATCH -p <partition_name>
#SBATCH -N 1
#SBATCH -n 1
#SBATCH -o %j.log
#SBATCH -e %j.err

set -e

echo "Job ID: $SLURM_JOB_ID"
echo "Partition: $SLURM_JOB_PARTITION"
echo "Node list: $SLURM_JOB_NODELIST"
echo "Submit dir: $SLURM_SUBMIT_DIR"
echo "Start time: $(date)"

hostname
srun hostname

echo "End time: $(date)"
```

提交：

```bash
sbatch test_slurm.sh
```

查看：

```bash
squeue -u $USER
```

## 4. 单节点 CPU 任务脚本

文件名：`cpu_job.sh`

```bash
#!/bin/bash
#SBATCH -J cpu_job
#SBATCH -p <partition_name>
#SBATCH -N 1
#SBATCH -n <num_tasks>
#SBATCH -o %j.log
#SBATCH -e %j.err

set -e

cd "$SLURM_SUBMIT_DIR"

echo "Job ID: $SLURM_JOB_ID"
echo "Node list: $SLURM_JOB_NODELIST"
echo "Start time: $(date)"

# 在这里替换成你的程序命令
# 例如：srun ./my_program input.dat
srun ./my_program input.dat

echo "End time: $(date)"
```

## 5. OpenMP 程序脚本

文件名：`openmp_job.sh`

```bash
#!/bin/bash
#SBATCH -J openmp_job
#SBATCH -p <partition_name>
#SBATCH -N 1
#SBATCH -n 1
#SBATCH -c <num_threads>
#SBATCH -o %j.log
#SBATCH -e %j.err

set -e

cd "$SLURM_SUBMIT_DIR"

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

echo "OMP_NUM_THREADS=$OMP_NUM_THREADS"
echo "Start time: $(date)"

./openmp_program input.dat

echo "End time: $(date)"
```

## 6. MPI 程序脚本

文件名：`mpi_job.sh`

```bash
#!/bin/bash
#SBATCH -J mpi_job
#SBATCH -p <partition_name>
#SBATCH -N <num_nodes>
#SBATCH -n <num_tasks>
#SBATCH -o %j.log
#SBATCH -e %j.err

set -e

cd "$SLURM_SUBMIT_DIR"

echo "Job ID: $SLURM_JOB_ID"
echo "Node list: $SLURM_JOB_NODELIST"
echo "Start time: $(date)"

srun ./mpi_program input.dat

echo "End time: $(date)"
```

多节点 MPI 作业涉及通信和 I/O，正式运行前应先用小规模任务测试扩展性。

## 7. 日志文件

如果脚本中写了：

```bash
#SBATCH -o %j.log
#SBATCH -e %j.err
```

任务运行后会生成类似：

```text
12345.log
12345.err
```

其中 `.log` 通常是标准输出，`.err` 通常是错误输出。

## 8. 常见任务状态

| 状态 | 含义 |
|---|---|
| `PD` | Pending，等待运行 |
| `R` | Running，正在运行 |
| `CG` | Completing，正在结束 |
| `CD` | Completed，已完成 |
| `F` | Failed，失败 |
| `TO` | Timeout，超时 |

## 9. 任务异常时建议保留的信息

向管理员反馈时，建议提供以下信息：

```bash
squeue -u $USER
scontrol show job JOBID
cat JOBID.err
cat JOBID.log
sacct -j JOBID --format=JobID,JobName,State,ExitCode,Elapsed,AllocCPUS,ReqMem,MaxRSS
```

公开截图前应遮挡主机名、内部路径、账号、节点名、分区名和其他敏感字段。

## 10. 使用建议

- 新程序先用小核数、小输入测试；
- 确认日志正常后再扩大规模；
- 长任务尽量写 checkpoint；
- 不要长期占满公共资源；
- 若需要长期大规模任务，建议提前与管理员或老师沟通。
