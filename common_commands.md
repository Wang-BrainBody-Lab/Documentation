- [Content](#content)
  * [Python](#python)
    + [Installing Conda](#installing-conda)
    + [Creating and Managing Environments with Conda](#creating-and-managing-environments-with-conda)
    + [Managing Environments More Efficiently](#managing-environments-more-efficiently)
    + [Example Workflow](#example-workflow)
    + [Managing package using pip](#managing-package-using-pip)
  * [Software](#software)
  * [SLURM](#slurm)
    + [Memory of SLURM jobs](#memory-of-slurm-jobs)
    + [Docker containers in SLURM jobs](#docker-containers-in-slurm-jobs)
  * [CPU status](#cpu-status)
  * [R](#r)
    + [Terminal](#terminal)
    + [Using RStudio Server](#using-rstudio-server)
  * [Git](#git)

# Content

## Python

https://packaging.python.org/en/latest/tutorials/installing-packages/

### Installing Conda

If you don't already have Conda installed, you can install it by downloading and installing Anaconda or Miniconda:

- **Anaconda**: A full distribution with many pre-installed packages.
- **Miniconda**: A minimal installation, allowing you to install only the packages you need.

You can download and install Miniconda from [here](https://docs.conda.io/en/latest/miniconda.html).

### Creating and Managing Environments with Conda

1. **Create a New Environment**:
   ```bash
   conda create --name project1 python=3.8
   ```

2. **Activate an Environment**:
   ```bash
   conda activate project1
   ```

3. **Deactivate the Current Environment**:
   ```bash
   conda deactivate
   ```

4. **List All Environments**:
   ```bash
   conda env list
   ```

5. **Remove an Environment**:
   ```bash
   conda remove --name project1 --all
   ```

6. **Install Packages in an Environment**:
   ```bash
   conda install --name project1 numpy pandas
   ```

7. **Export an Environment**:
   This is useful for sharing environments or for reproducing them on different systems.
   ```bash
   conda env export --name project1 > environment.yml
   ```

8. **Create an Environment from an Environment File**:
   ```bash
   conda env create --file environment.yml
   ```

### Managing Environments More Efficiently

1. **Cloning an Environment**:
   You can clone an existing environment to create a new one with the same packages.
   ```bash
   conda create --name project2 --clone project1
   ```

2. **Updating an Environment**:
   Update all packages in the current environment to the latest versions.
   ```bash
   conda update --all
   ```

3. **Specifying Channels**:
   Sometimes, certain packages are available in specific channels. You can specify channels when installing packages.
   ```bash
   conda install -c conda-forge somepackage
   ```

### Example Workflow

1. **Create and Activate a New Environment**:
   ```bash
   conda create --name myenv python=3.9
   conda activate myenv
   ```

2. **Install Required Packages**:
   ```bash
   conda install numpy pandas scipy
   ```

3. **Export the Environment**:
   ```bash
   conda env export > myenv.yml
   ```

4. **Deactivate and Reactivate**:
   ```bash
   conda deactivate
   conda activate myenv
   ```

5. **Remove the Environment**:
   ```bash
   conda remove --name myenv --all
   ```

### Managing package using pip
```bash
# Install package using pip
python3 -m pip install --upgrade pip setuptools wheel
python3 -m pip install "SomeProject"

# Copy files
cp -r /home/packageA/. /home/cp/packageB/

(Before setting up the package, enter the directory where the setup.py is first)
# Set up a package from GitHub
python setup.py install

# Reset
pip uninstall package
python setup.py install

```

## Software
```

3D cellpose: python -m cellpose --Zstack 
```

## [SLURM](https://biohpc.cornell.edu/lab/SLURM-on-demand.htm#interactivejobs)
```bash
# Check status/job: 
sacct
sacct -j jobID
scontrol show job jobID

# Request for allocate resource:
salloc --job-name=sw2395 --time=02:00:00 --ntasks=1 --cpus-per-task=256 --mem=700G 
```

Once the resources are allocated, you'll can enter an interactive session where you can run commands. For example:

```bash
sacct
# Enter existing shell
srun --job-name=sw2395 --time=02:00:00 --ntasks=1 --cpus-per-task=256 --mem=700G --pty bash
# This will print the default shell for your user, but not necessarily the shell you are currently using if you've changed it within the session.
echo $SHELL
# This command prints the name of the current shell or script.
echo $0
# Terminate a job
scancel jobid
```
Shell scripts example usage:
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

### Memory of SLURM jobs
If you don't how many memory you will use. To determine the memory needs of your job, follow these steps:

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

### Using RStudio Server

If you prefer a graphical interface, you might consider setting up RStudio Server on your remote machine.
```bash
# Download and install RStudio Server:

sudo apt-get install gdebi-core
wget https://download2.rstudio.org/server/bionic/amd64/rstudio-server-1.4.1106-amd64.deb
sudo gdebi rstudio-server-1.4.1106-amd64.deb

# Start RStudio Server:
sudo rstudio-server start

# Access RStudio Server via your web browser by navigating to http://remote_server_address:8787.
```
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
