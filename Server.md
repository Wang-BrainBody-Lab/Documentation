- [Server and file access](#server-and-file-access)
  * [FileZilla](#filezilla)
  * [Mount the server](#mount-the-server)
  * [Command Prompt](#command-prompt)
  * [VSCode](#vscode)
- [SLURM](#slurm)
  * [Interactive](#interactive)
  * [Submit a job using shell scripts](#submit-a-job-using-shell-scripts)
  * [Interactive SCREEN Session](#interactive-screen-session)
  * [Memory usage of SLURM jobs](#memory-usage-of-SLURM-jobs)
  * [Docker containers in SLURM jobs](#docker-containers-in-slurm-jobs)
- [CPU status](#cpu-status)
- [R](#r)
  * [Terminal](#terminal)
- [Git](#git)

---
## Server and file access
There are several ways to access the server. For file management, we use FileZilla or directly mount the server. For running commands, we use Command Prompt or VSCode. 
**Note:** You must connect to Cornell campus network or use [CU VPN](https://it.cornell.edu/cuvpn#toc-who-can-us-Ikb_bdqc) to use server.

### FileZilla
FileZilla is a tool to manage files on the servers, you can use it for common file management, such as move, copy, download or upload.

You can download [Filezilla](https://filezilla-project.org/) for free.

File transfer settings with FileZilla:
- Host: sftp://cbsuwsun.biohpc.cornell.edu
- Port: 22

### Mount the server

for Windows 10 (and above) users, you can also choose to mount the server's directory to your local computer. In this way, you can open/visualize server's files locally. How to set up:

- In `File Explorer` click on `This PC` , select `Computer` at the top left of the window,
then click on `Map network drive`.
- Choose the Drive letter and enter the server address for the folder that you wish to map:
```
\\cbsuwsun.biohpc.cornell.edu\storage (apart from storage, you can also mount docker/local_data/workdir)
```
- Make sure that the check box for `Connect using different credentials` is checked, then click `Finish`
- When prompted for your network credentials, enter BioHPC\NetId as your user name,
and use your BioHPC password.
- If successful, you should find the new network location under `This PC` .

### Command Prompt
For windows users, you can access the server through Command Prompt (cmd) directly using:
```
  SSH cbsuwsun.biohpc.cornell.edu
```

For MacOS and Linux users, you can access the server through the terminal directly using:
```
  ssh netID@cbsuwsun.biohpc.cornell.edu
```
### VSCode
Visual Studio Code (VSCode) is one of the most popular IDEs, and it has become a go-to tool for developers working with remote servers via SSH. Apart from common functionality of IDE, here are what it can do for SSH: SSH connectivity, file management, file editing, and extension support.

You can download [VSCode](https://code.visualstudio.com/) for free.

To use VSCode's SSH functionality, follow these steps:

1. Install the "Remote - SSH" extension in VSCode.
2. Once installed, you'll see a new "Remote Explorer" icon in the sidebar.
3. In the Remote Explorer, locate the SSH section and click the gear icon to open the SSH configuration file.
4. Edit the `.ssh/config` file (note the forward slash) with your server details as follows:

```
Host cbsuwsun.biohpc.cornell.edu
  HostName cbsuwsun.biohpc.cornell.edu
  User YourUsername
```
Replace "YourUsername" with your Cornell netID.

5. Now the server (cbsuwsun.biohpc.cornell.edu) should appear under the SSH section, click the "->" icon and type your password in the Command Palette to connect to the server. 
6. Once connected, you can access files on the server and run scripts on the server via VSCode.

You can find the detailed instruction [here](https://code.visualstudio.com/docs/remote/ssh)

## SLURM
Official documentation from BioHPC is available [here](https://biohpc.cornell.edu/lab/SLURM-on-demand.htm). 

### Interactive

**Check status/job** 
```bash
sacct
sacct -j jobID
```
**Check the details of a job**
```bash
scontrol show job jobID
```
**Check current jobs**
```bash
squeue
```
**Request for allocate resource**
```bash
salloc --job-name=sw2395 --time=02:00:00 --ntasks=1 --cpus-per-task=256 --mem=700G
```
**Once the resources are allocated, you can start an interactive session**
```bash
srun --job-name=sw2395 --time=02:00:00 --ntasks=1 --cpus-per-task=256 --mem=700G --pty bash
```
**Print the default shell for your user (not necessarily the shell you are currently using if you've changed it within the session)**
```bash
echo $SHELL
```
**Prints the name of the current shell or script**
```bash
echo $0
```
**Terminate a job**
```bash
scancel jobid
```
**Cancel all jobs by a user**
```bash
scancel -u <my_user_name>
```
### Submit a job using shell scripts
```bash
#!/bin/bash -l                
#SBATCH --nodes=1                
#SBATCH --ntasks=1               
#SBATCH --cpus-per-task=256
#SBATCH --mem=950G              
#SBATCH --time=8:00:00      
#SBATCH --partition=regular     
#SBATCH --job-name=sw2395      
#SBATCH --mail-user=XX@cornell.edu 
#SBATCH --mail-type=ALL     
#SBATCH --output=slurm-%j.out  
#SBATCH --error=slurm-%j.err 

# Execute the script
python codes/predict.py
```
[Slurm Job Builder](https://docs.rcd.clemson.edu/palmetto/job_management/job_builder/) can be used to customize the scripts above. 
### Interactive SCREEN Session
```bash
sbatch -N 1 <other_options>  /programs/bin/slurm_screen.sh
```
SCREEN documentation can be found [here](https://biohpc.cornell.edu/lab/SLURM-on-demand.htm#interactivejobs).
```bash
sbatch -N 1 --ntasks=1 --time=48:00:00 --mem=4G --job-name=screen_job /programs/bin/slurm_screen.sh
```
`/programs/bin/slurm_screen.sh` file is already there, you don't have to change it.

Use `screen -ls` to verify the SCREEN session has been started there. Attach to this session using `screen -r`. Within the session, you can start any number of shells you need and run any processes you need. All the processes together will share the CPUs and memory specified at job submision. 

### Memory usage of SLURM jobs
If you don't know how much memory you will use. You can determine the memory needs of your job with the following steps:

1. **Run Jobs with Increasing Memory:**
   - Submit jobs with gradually increasing memory.
   - Use `squeue` to find the job ID of the first job that doesn't crash.

2. **Check Memory Usage:**
   - After the job finishes, run:
     ```
     sacct -j <jobID> --format=JobID,User,ReqMem,MaxRSS,MaxVMSize,NCPUS,Start,TotalCPU,UserCPU,Elapsed,State%20
     ```
   - Look at the `MaxRSS` column for the maximum memory used.

3. **Adjust Memory Request:**
   - If `MaxRSS` is close to the requested memory, the job may be using swap space, which slows it down.
   - To prevent this, add `ulimit -v $(ulimit -m)` after the `#SBATCH` lines in your script to limit virtual memory to physical memory. This will cause the job to crash instead of thrashing if it exceeds physical memory, indicating more memory is needed.

4. **Evaluate Resource Usage:**
   - Use `get_slurm_usage.pl` to analyze resource usage over a period. For example:
     ```
     get_slurm_usage.pl mysrvr 01/20/20 3
     ```
   - Compare the requested memory (`avReqMem`) with the actual memory used (`avUsedMem`). If `avUsedMem` is much smaller, you’ve been requesting too much memory.


### Docker containers in SLURM jobs

Docker containers can be started from within a SLURM job using the docker1 command. However, such containers will not automatically obey the CPU, memory, or time allocations granted to a job by SLURM. Therefore, these limits have to be imposed explicitly on the container. For example, if a job is submitted with SLURM options --mem=42G -n 4, these restrictions must be passed on to the docker container via the docker1 command as follows:

`docker1 run  --memory="40g" --cpus=4  <image_name> <command> `

(note that the memory made available to the conatiner should be somewhat smaller than the amount requested from SLURM). If neither of the --mem or -n (same as --ntasks) SLURM options are explicitly specified at job submittion, the defaults are --mem=1G and -n 1, respectively, and these should be used in docker1 command above.

 There is currently no tested way of imposing a time limit on docker containers.

## CPU status
Here are a few useful commands to check CPU status on the server:  
```bash
# View real-time system resource usage
top/htop

# View CPU information
lscpu

# View memory usage
free -h

# View the number of processing units
nproc
```

## R
### Terminal
`R --version`

Install renv in your project:

`install.packages("renv")`

Initialize renv in your project directory:

`renv::init()`

You can run R scripts directly in the terminal. For example, to run a script called my_script.R:

`Rscript my_script.R`

To install R packages, you can use R's package management system. For example:

`install.packages("ggplot2")`

## [Git](https://git-scm.com/docs/gittutorial)
```
git clone
git status
git add
git add --all
git commit -m 
git push
git merge
```
