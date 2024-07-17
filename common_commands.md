- [Content](#content)
  * [Python](#python)
  * [Software](#software)
  * [SLURM](#slurm)
  * [CPU status](#cpu-status)
  * [R](#r)
    + [Terminal](#terminal)
    + [Using RStudio Server](#using-rstudio-server)

# Content
## Python
https://packaging.python.org/en/latest/tutorials/installing-packages/
```bash
# Create virtual environment
python -m venv ~/Desktop/stardist
active venv: source bin/activate
deactivate: deactivate

# Install package
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

## SLURM
```bash
# Check status/job: 
sacct
sacct -j jobID
scontrol show job jobID

# Request for allocate resource:
salloc --job-name=sw2395 --time=02:00:00 --ntasks=1 --cpus-per-task=256 --mem=700G 
```

Once the resources are allocated, you'll be in an interactive session where you can run commands. For example:

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
