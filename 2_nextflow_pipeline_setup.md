- [Nextflow](#nextflow)
- [Nextflow Tower Agent](#nextflow-tower-agent)
  * [Installation](#installation)
  * [Quickstart](#quickstart)
- [Run pipeline from Seqera](#run-pipeline-from-seqera)
  * [1. Create Slurm Workload Manager](#1-create-slurm-workload-manager)
  * [2. Add Pipeline in Nextflow Workspace](#2-add-pipeline-in-nextflow-workspace)
  * [3. Configure Advanced Options](#3-configure-advanced-options)
  * [4. Configure Pipeline Parameters](#4-configure-pipeline-parameters)
- [Run pipeline locally](#run-pipeline-locally)
 
## Nextflow
Before using the tower agent, please download and set up the nextflow first. If you would like to use NextFlow on the server, make sure you log onto the bioHPC server first before installing and configuring Nextflow. 

1. Download the [nextflow](https://www.nextflow.io/docs/latest/install.html) by `curl -s https://get.nextflow.io | bash`
   Nextflow requires Java 17 (or later, up to 25) to be installed. If you don’t have a compatible version of Java installed, please see instructions [here](https://www.nextflow.io/docs/latest/install.html) to install.
3. Make it executable: `chmod +x nextflow`
4. Move it into a folder that is in your $PATH, or put the current folder in `~/.bashrc` by:
   ```
   nano ~/.bashrc
   export PATH="YourFolderPath:$PATH"
   source ~/.bashrc
   ```
5. You may also need to add the following to the `~/.bashrc` for Singularity to work.
   ```
   export APPTAINER_TMPDIR=YourPath/temp/tmpdir
   export APPTAINER_CACHEDIR=YourPath/temp/cachedir
   ```
   **Note:** You may need to manually create the folders before running the pipeline.

## Nextflow Tower Agent
### [Installation](https://docs.seqera.io/platform/23.1.0/agent#installation "Direct link to Installation")

Click the "Installation" for more details.

Tower Agent is distributed as a single executable file to simply download and execute.

1. Download the latest release from [Github](https://github.com/seqeralabs/tower-agent):
    
    ```
    curl -fSL https://github.com/seqeralabs/tower-agent/releases/latest/download/tw-agent-linux-x86_64 > tw-agent
    ```
    
2. Make it executable:
    
    ```
    chmod +x ./tw-agent
    ```
    

### [Quickstart](https://docs.seqera.io/platform/23.1.0/agent#quickstart "Direct link to Quickstart")

Click the "Quickstart" for more details.

Before running the Agent:

1. Create a [**personal access token**](https://docs.seqera.io/platform/23.1.0/api/overview#authentication) in Tower. Your personal authorization token can be found in the user top-right menu under [Your tokens](https://cloud.seqera.io/tokens).
2. Create **Tower Agent** credentials in a Tower workspace. See [here](https://docs.seqera.io/platform/23.1.0/credentials/overview) for more instructions.
   (This step may not be necessary) If running on the bioHPC server, a [SSH credential](https://docs.seqera.io/platform-cloud/credentials/ssh_credentials) needs to be created.  
4. When you create the credentials you'll get an **Agent Connection ID**. You can use the default ID or enter a custom ID — the connection ID in the workspace credentials must match the ID entered when you run the agent.
5. After getting the Agent Connection ID, running the tower on server's terminal with the following commands, replace with your real Token and ID.
```
export TOWER_ACCESS_TOKEN=<YOUR TOKEN>  
./tw-agent <YOUR CONNECTION ID> --work-dir= <YOUR WORK DIRECTORY>
```

Nextflow Tower Agent should always be running in order to accept incoming requests from Tower. We recommend that you use a terminal multiplexer Screen, so that it keeps running even if you close your SSH session.

Follow the steps below to start an agent on the server: 

- To use screen, create a `screen_job.sh` file in your home directory with the following scripts, replace the Token and ID with true values.
  
```bash
#!/bin/bash
#SBATCH --job-name=screen_job
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --time=48:00:00
#SBATCH --mem=4G
#SBATCH --partition=regular

# Create a unique name for this screen session
SCREEN_NAME="slurm_job_$SLURM_JOB_ID"

# Start a detached screen session
screen -wipe
screen -dmS $SCREEN_NAME

# Function to send commands to the screen session
send_command() {
    screen -S $SCREEN_NAME -X stuff "$1"
}

# Replace <YOUR TOKEN> and <YOUR CONNECTION ID> with actual values
TOWER_ACCESS_TOKEN=<YOUR TOKEN>
CONNECTION_ID=<YOUR CONNECTION ID>

# Send commands to the screen session
send_command "export TOWER_ACCESS_TOKEN=$TOWER_ACCESS_TOKEN\n"
send_command "./tw-agent \"$CONNECTION_ID\" --work-dir=./work\n"

# Keep the SLURM job running until the time limit is reached or the screen session ends
while screen -list | grep -q $SCREEN_NAME
do
    sleep 360
done

echo "Screen session ended. SLURM job complete."
```

- After creating `screen_job.sh`, please use `chmod +x screen_job.sh` to make it executable.
- Execute using `sbatch screen_job.sh`. 
- The nextflow tower agent is online now!
- You can use `screen -r` or `srun --jobid $SLURM_JOB_ID --pty screen -r slurm_job_$SLURM_JOB_ID` to attach the SCREEN sessions, and detach from it at any time with `Ctrl-A` followed by `D`.
- To see your screen sessions: `screen -ls`
- To quit any screen session: `screen -S <session ID> -X quit`
- If you cancel the corresponding SLURM job using `scancel $SLURM_JOB_ID`, the screen session will also be terminated.
- If you don't need screen seesion anymore, please cancel the corresponding SLURM job manually.

## Running pipeline from Seqera
### 1. Add Compute Environment

Under COMPUTE -> Compute Environment, click "Add compute enviroment". 
Choose "Slurm Workload Manager" as platform, add the **Tower Agent** credentials created above. Click "Add" to create the compute environment. 

### 2. Add Pipeline in Nextflow Workspace

1. **Access the Launchpad:**
    - Go to your Nextflow launchpad.
2. **Add a New Pipeline:**
    - Click on `Add Pipeline`.
3. **Choose Compute Environments:**
    - Select `Compute Environments` you just created.
4. **Specify the "Pipeline to Launch":**
    - Enter the path to the Multifish pipeline on GitHub (https://github.com/Neall37/multifish).
    - Check the box for `Pull latest` to ensure you get any updates.
Click "Add" to add the pipeline. Now you are ready to launch.

To launch a run, selecte the pipeline and click "Launch" and configure Pipeline parameters to launch the run. 

### 3. Configure Pipeline Parameters

There are two ways to configure pipeline parameters. One is to upload params file as a JSON FILE. You can download the `example_slurm.json` file from GitHub, which contains necessary parameters. You can then edit the parameters according to your requirements and upload the edited JSON file. Alternatively, you can enter the parameters directly in the interface. 


