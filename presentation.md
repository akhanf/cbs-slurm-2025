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

**7 high-performance compute nodes** with:
- **128 CPU cores** each
- **500+ GB RAM** per node
- **2× NVIDIA L40S GPUs** per node
- **7TB local SSD** per node

---

# What is SLURM?

**S**imple **L**inux **U**tility for **R**esource **M**anagement

- A **job scheduler** that manages when, where, and how jobs run
- Allocates resources (CPU, memory, GPU, time) fairly
- Executes workloads on compute nodes
- Ensures **fairness**, **efficiency**, and **stability** in multi-user environments

---

# Why Use SLURM?

- **Fair resource sharing** among all users
- **Efficient job scheduling** and queuing
- **Resource optimization** for better performance
- **Job monitoring** and management
- Work submitted as **batch jobs** or **interactive sessions**

---

# Accessing the Cluster

Connect to the **master node**:

```bash
ssh your_uwo_username@rri-cbs-slurm.fmd.uwo.pri
```

⚠️ **NEVER run computations on the master node!**
- Master node is only for **job submission, file management, and light setup**
- All computational work must be submitted via SLURM

💡 **Access Requirements:**
- Direct SSH only via CBS VDI servers or UWO network
- Off-campus: Connect through CBS VDI servers
- Use `-X` flag for X11 forwarding if needed

---

# Cluster Architecture

## Master Node
**rri-cbs-slurm.fmd.uwo.pri** - SSH access, job submission only
- 2× Intel® Xeon® Gold 6230R @ 2.10GHz
- 8 GB Memory

## Compute Nodes  
**rri-cbs-h1** → **rri-cbs-h7** - Where jobs actually run
- Intel® Xeon® Gold 6448Y (128 logical CPUs)
- ~504 GB RAM
- 2× NVIDIA L40S GPUs (46 GB each)
- 7 TB SSD local scratch

⚠️ **Never run heavy computations on the master node!**

---

# Submitting a Job

Jobs are submitted using:

```bash
sbatch my_job_script.sh
```

The script describes:
1. Resources you need (CPUs, memory, GPUs, runtime)
2. Commands to run

⚠️ **Important Limits:**
- **Maximum job time: 48 hours**
- Design code with **checkpoints** for longer workflows
- Interactive sessions limited to **5 hours**

---

# Basic SLURM Commands

```bash
# Submit a job
sbatch my_script.sh

# View queue
squeue

# View your jobs
squeue -u your_uwo_username

# Cancel a job
scancel <job_id>

# Job information
scontrol show job <job_id>

# Job history
sacct -u your_uwo_username

# List nodes
sinfo
```

---

# Job Script Template

```bash
#!/bin/bash
#SBATCH --job-name=myanalysis
#SBATCH --time=12:00:00
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G
#SBATCH --gpus-per-node=0
#SBATCH --output=slurm-%j.out

# Load software modules
module load python/3.13.2

# Copy input data to local scratch (/tmp)
cp /nfs/smith/myproject/data/input.csv /tmp

# Run computation
python myscript.py /tmp/input.csv > /tmp/results.txt

# Copy results back to lab share
cp /tmp/results.txt /nfs/smith/myproject
```

---

# Resource Requests

## Key SBATCH Flags

- `--job-name`: Job name
- `--time`: Runtime limit (max 48:00:00)
- `--mem`: Memory per node (e.g., 8G, 16G)
- `--cpus-per-task`: Number of CPU cores
- `--gpus-per-node`: Request GPU(s)
- `--gpus-per-node=l40s:2`: Request specific GPUs
- `--output`: Save output log (slurm-%j.out)

💡 **GPU Types:**
- Currently: `l40s` (NVIDIA L40S)
- Coming soon: `a100` GPUs

---

# Interactive Jobs

For testing and debugging (max **5 hours**):

```bash
# Basic interactive session
salloc --time=05:00:00 --nodes=1 --ntasks=1 \
       --cpus-per-task=2 --mem=8G

# With GPU
salloc --time=05:00:00 --gpus-per-node=1

# With X11 forwarding (for GUI apps)
salloc --time=05:00:00 --mem=8G --x11
```

Remember to connect with `-X` for X11:
```bash
ssh -X your_uwo_username@rri-cbs-slurm.fmd.uwo.pri
```

---

# Best Storage Practices

**Typical Workflow:**

1. **Copy data** from `/nfs/<pi_last_name>` or `/nfs/scratch` to `/tmp`
2. **Process data** using `/tmp` (fastest I/O)
3. **Checkpoint** outputs periodically to `/nfs/scratch` (optional but recommended)
4. **Copy final results** from `/tmp` back to `/nfs/<pi_last_name>` before job exits

💡 **Remember:** `/tmp` is deleted when your job completes!

📧 **Request Storage:** Contact support-cbs-server@uwo.ca for lab shares

---

# Software Modules (Lmod)

The cluster uses **modules** to manage software:

```bash
# List available software
module avail

# Load a module
module load freesurfer/7.4.1
module load python/3.13.2

# Check loaded modules
module list

# Unload a module
module unload freesurfer/7.4.1
```

Load modules in your job script or interactive session to access software.

---

# Monitoring Jobs

```bash
# Check job status
squeue -u your_uwo_username

# Detailed job info
scontrol show job <job_id>

# Job history
sacct -u your_uwo_username

# List cluster nodes
sinfo
```

---

# Using kslurm Wrapper

[**kslurm**](https://kslurm.readthedocs.io/en/latest/) simplifies SLURM commands:

**kbatch** - Batch submission (no script needed):
```bash
# 12hr job, 16 cores, 24GB memory
kbatch 12:00 16 24G recon-all <recon-all-args>
```

**krun** - Interactive session:
```bash
# 4 cores, 3hr, 15GB memory, with GPU
krun 4 3:00 15G gpu
```

No need for confusing `--arguments` or script files!

See full [documentation](https://kslurm.readthedocs.io/en/latest/) for details.

---

# GPU Jobs

Request GPUs in your job script:

```bash
#!/bin/bash
#SBATCH --job-name=gpu_job
#SBATCH --gpus-per-node=1
#SBATCH --mem=16G
#SBATCH --cpus-per-task=4
#SBATCH --time=12:00:00

# Load required modules
module load cuda/12.8

# Run GPU program
./my_cuda_program
```

**GPU Types:**
- Currently: `l40s` (2× per node, 46GB each)
- Coming: `a100` GPUs

---

# Best Practices

✅ **DO:**
- Request only resources you need
- Use `/tmp` for fast I/O operations
- Copy data to `/tmp` before processing
- Design code with checkpoints (48hr max)
- Save results before job completes
- Use kslurm for simplified commands

❌ **DON'T:**
- Run computations on master node
- Exceed 48 hours (batch) or 5 hours (interactive)
- Leave data in `/tmp` (it's deleted!)
- Forget to copy results back to `/nfs/...`

---

# Storage: Data Locations

## 🔒 Lab Shares: `/nfs/<pi_last_name>`
- Dedicated fileshare for project data
- Accessible from all compute nodes
- Stored on 2025 OneFS fileserver

## 📂 Shared Scratch: `/nfs/scratch`
- 25 TB shared temporary space
- For staging input/output data
- ⚠️ Files deleted after **30 days**
- **Not backed up**

## ⚡ Local Scratch: `/tmp`
- 7TB fast SSD per compute node
- Private to your job, auto-created
- **Deleted when job completes**
- Fastest option for I/O intensive work

---

# Access & Billing

## Access Requirements
- **CBS SLURM is for Power users only**
- Direct SSH via CBS VDI or UWO network
- Off-campus: Connect through CBS VDI servers

## Storage
- Only supports OneFS storage (`/nfs/...`)
- Legacy `/cifs/...` shares not accessible from compute nodes
- Contact support-cbs-server@uwo.ca for lab shares

## Resources
- [CBS Servers Documentation](https://hackmd.io/@CompCore/cbs_servers)
- [CBS Storage Guide](https://hackmd.io/@CompCore/cbs_storage)

---

# Troubleshooting

## Common Issues

- **Job pending**: Check resource availability with `sinfo`
- **Out of memory**: Increase `--mem`
- **Time limit exceeded**: Increase `--time` (max 48:00:00)
- **Job failed**: Check output logs (`slurm-<job_id>.out`)
- **Cannot access data**: Ensure using `/nfs/...` not `/cifs/...`

```bash
# Why is my job pending?
squeue -u your_uwo_username
```

---

# Additional Resources

## Documentation
- [Alliance Canada - What is a scheduler?](https://docs.alliancecan.ca/wiki/What_is_a_scheduler)
- [Alliance Canada - Running jobs](https://docs.alliancecan.ca/wiki/Running_jobs)
- [Alliance Canada - Using GPUs with Slurm](https://docs.alliancecan.ca/wiki/Using_GPUs_with_Slurm)
- [Alliance Canada - MATLAB](https://docs.alliancecan.ca/wiki/MATLAB)
- [kslurm Documentation](https://kslurm.readthedocs.io/en/latest/)

## CBS Resources
- [CBS Servers Guide](https://hackmd.io/@CompCore/cbs_servers)
- [CBS Storage Guide](https://hackmd.io/@CompCore/cbs_storage)

---

# Quick Reference

```bash
# Job Submission
sbatch script.sh              # Submit batch job
salloc --time=5:00:00 ...     # Interactive session (max 5hr)
kbatch 12:00 16 24G command   # kslurm batch
krun 4 3:00 15G gpu           # kslurm interactive

# Monitoring
squeue -u your_uwo_username   # Your jobs
sinfo                         # Cluster status
sacct -u your_uwo_username    # Job history
scancel <job_id>              # Cancel job

# Software & Storage
module avail/load/list        # Manage software
/nfs/<pi_last_name>           # Lab share
/nfs/scratch                  # Temporary (30 days)
/tmp                          # Fast local (deleted on exit)
```

---

# Next Steps

1. **Connect** to the cluster
   ```bash
   ssh your_uwo_username@rri-cbs-slurm.fmd.uwo.pri
   ```
2. **Check** available resources
   ```bash
   sinfo
   module avail
   ```
3. **Submit** a test job (start small!)
4. **Monitor** your job with `squeue -u your_uwo_username`
5. **Read** the documentation and explore kslurm
6. **Contact** support-cbs-server@uwo.ca for help

---

# Questions?

## Thank You!

**Contact Information:**
- 📧 Email: support-cbs-server@uwo.ca
- 📚 Documentation: 
  - [CBS Servers](https://hackmd.io/@CompCore/cbs_servers)
  - [Alliance Canada SLURM Docs](https://docs.alliancecan.ca/wiki/What_is_a_scheduler)

**Happy Computing! 🚀**
