---
marp: true
theme: default
paginate: true
backgroundColor: #fff
backgroundImage: url('https://marp.app/assets/hero-background.svg')
header: 'CBS SLURM Cluster Introduction'
footer: 'Computational Core - CBS'
---

# Welcome to CBS SLURM

## High-Performance Computing at CBS

---

# What is SLURM?

**S**imple **L**inux **U**tility for **R**esource **M**anagement

- Open-source workload manager
- Job scheduling system
- Resource allocation
- Used by many supercomputers worldwide

---

# Why Use SLURM?

- **Fair resource sharing** among all users
- **Efficient job scheduling** and queuing
- **Resource optimization** for better performance
- **Job monitoring** and management
- **Scalability** for large workloads

---

# Getting Started

## Accessing the Cluster

1. Connect via SSH
2. Use your CBS credentials
3. Check your environment

```bash
# SSH connection
ssh username@slurm.cbs.example.edu

# Check your account
sinfo
squeue -u $USER
```

---

# SLURM Architecture

## Key Components

- **Login Nodes**: Where you submit jobs
- **Compute Nodes**: Where jobs run
- **Head Node**: Manages the cluster

⚠️ **Never run heavy computations on login nodes!**

---

# Understanding Partitions

Partitions are like queues with different resources:

- **short**: Quick jobs (<4 hours)
- **medium**: Standard jobs (<24 hours)
- **long**: Extended jobs (<7 days)
- **gpu**: GPU-accelerated jobs
- **highmem**: High-memory jobs

```bash
sinfo -s  # View available partitions
```

---

# Basic SLURM Commands

```bash
# Submit a job
sbatch my_script.sh

# View queue
squeue

# View your jobs
squeue -u $USER

# Cancel a job
scancel <job_id>

# Job information
scontrol show job <job_id>
```

---

# Job Script Basics

```bash
#!/bin/bash
#SBATCH --job-name=my_job
#SBATCH --time=01:00:00
#SBATCH --mem=4G
#SBATCH --cpus-per-task=4
#SBATCH --output=job_%j.out
#SBATCH --error=job_%j.err

# Your commands here
echo "Job started at $(date)"
./my_program
echo "Job finished at $(date)"
```

---

# Resource Requests

## Key Parameters

- `--time`: Maximum runtime (HH:MM:SS)
- `--mem`: Memory per node (e.g., 8G, 16G)
- `--cpus-per-task`: Number of CPU cores
- `--nodes`: Number of nodes
- `--partition`: Which queue to use
- `--gres=gpu:1`: Request GPU resources

---

# Interactive Jobs

For testing and debugging:

```bash
# Start interactive session
srun --pty --mem=4G --time=1:00:00 bash

# Interactive with GPU
srun --pty --gres=gpu:1 --mem=8G --time=2:00:00 bash

# Exit when done
exit
```

---

# Monitoring Jobs

```bash
# Check job status
squeue -u $USER

# Detailed job info
scontrol show job <job_id>

# Job history
sacct -u $USER

# Job efficiency report
seff <job_id>
```

---

# Output and Error Files

By default:
- `slurm-<job_id>.out` - Standard output
- Customize with `--output` and `--error`

```bash
#SBATCH --output=logs/job_%j.out
#SBATCH --error=logs/job_%j.err
```

Use `%j` for job ID, `%x` for job name

---

# Environment Modules

Load software with modules:

```bash
# List available modules
module avail

# Load a module
module load python/3.9

# List loaded modules
module list

# Unload a module
module unload python/3.9
```

---

# Array Jobs

Run multiple similar jobs efficiently:

```bash
#!/bin/bash
#SBATCH --array=1-10
#SBATCH --job-name=array_job

# Use $SLURM_ARRAY_TASK_ID
echo "Processing task $SLURM_ARRAY_TASK_ID"
./process_file_${SLURM_ARRAY_TASK_ID}.sh
```

---

# Dependencies

Chain jobs together:

```bash
# Submit first job
job1=$(sbatch --parsable first_job.sh)

# Submit dependent job
sbatch --dependency=afterok:$job1 second_job.sh
```

Dependency types: `afterok`, `afterany`, `afternotok`

---

# GPU Jobs

```bash
#!/bin/bash
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --mem=16G
#SBATCH --cpus-per-task=4

# Load CUDA module
module load cuda/11.8

# Run GPU program
./my_cuda_program
```

---

# Best Practices

✅ **DO:**
- Request only resources you need
- Test with short jobs first
- Use array jobs for similar tasks
- Monitor job efficiency with `seff`
- Clean up old output files

❌ **DON'T:**
- Run computations on login nodes
- Request excessive resources
- Submit thousands of tiny jobs
- Leave interactive sessions idle

---

# Storage Considerations

- **Home**: `/home/$USER` (backed up, limited quota)
- **Scratch**: `/scratch/$USER` (large, temporary)
- **Project**: `/project/group_name` (shared storage)

⚠️ Files in `/scratch` may be deleted after 30 days

---

# Troubleshooting

## Common Issues

- **Job pending**: Check resource availability
- **Out of memory**: Increase `--mem`
- **Time limit**: Increase `--time`
- **Job failed**: Check error logs

```bash
# Why is my job pending?
squeue -j <job_id> --start
```

---

# Getting Help

## Resources

- Documentation: Check cluster wiki/docs
- Example scripts: `/opt/examples/`
- Support: Email support team
- Office hours: Check schedule

```bash
# Check cluster status
sinfo

# Contact info in MOTD
cat /etc/motd
```

---

# Quick Reference

```bash
sbatch script.sh          # Submit batch job
srun command              # Run interactive job
squeue                    # View queue
scancel <job_id>          # Cancel job
sinfo                     # Cluster info
sacct                     # Job history
seff <job_id>             # Job efficiency
module avail/load/list    # Software modules
```

---

# Next Steps

1. **Log in** to the cluster
2. **Run a test job** (start small!)
3. **Review your job efficiency** with `seff`
4. **Explore modules** for your software
5. **Read the documentation** for advanced features
6. **Ask for help** when needed

---

# Questions?

## Thank You!

**Contact Information:**
- Email: support@cbs.example.edu
- Documentation: https://docs.cbs.example.edu
- Office Hours: Wednesdays 2-4 PM

**Happy Computing! 🚀**
