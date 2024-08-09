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
Before using the tower agent, please download and set up the nextflow first:

1. Download the [nextflow](https://www.nextflow.io/docs/latest/install.html) by `curl -s https://get.nextflow.io | bash`
2. Make it executable: `chmod +x nextflow`
3. Move it into a folder that is in your $PATH, or put the current folder in `~/.bashrc` by:
   ```
   nano ~/.bashrc
   export PATH="YourFolderPath:$PATH"
   source ~/.bashrc
   ```
4. You may also need to add the following to the `~/.bashrc` for Singularity to work.
   ```
   export APPTAINER_TMPDIR=YourPath/temp/tmpdir
   export APPTAINER_CACHEDIR==YourPath/temp/cachedir
   ```
   **Note:** You may need to manually create the files before running the pipeline.

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
3. When you create the credentials you'll get an **Agent Connection ID**. You can use the default ID or enter a custom ID — the connection ID in the workspace credentials must match the ID entered when you run the agent.
4. After getting the Agent Connection ID, running the tower on server's terminal by following, replace with your real Token and ID.
```
export TOWER_ACCESS_TOKEN=<YOUR TOKEN>  
./tw-agent <YOUR CONNECTION ID> --work-dir= <YOUR WORK DIRECTORY>
```
5. Go back to seqera and click `Add` in seqera to create a credential.

The agent should always be running in order to accept incoming requests from Tower. We recommend that you use a terminal multiplexer Screen, so that it keeps running even if you close your SSH session.

Input the following command in your terminal, 


To use screen, run the following `screen_job.sh` script using `sbatch screen_job.sh`instead, replace the Token and ID with true value.

After creating `screen_job.sh`, please use `chmod +x screen_job.sh` to make it executable.

You can use `screen -r` to attach the SCREEN sessions, and detach from it at any time with `Ctrl-A` followed by `D`.

- To see your screen sessions: `use screen -ls`
- To quit any screen session: `screen -S <session ID> -X quit`
- If you cancel the corresponding SLURM job using `scancel $SLURM_JOB_ID`, the screen session will also be terminated.
- So once you don't need screen seesion anymore, please cancel the corresponding SLURM job manually.

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

The nextflow tower agent is online now!

## Run pipeline from Seqera
### 1. Create Slurm Workload Manager


1. **Setup Slurm Workload Manager:**
    
    - Access your compute environment and set up Slurm with the generated credentials.

### 2. Add Pipeline in Nextflow Workspace

1. **Access the Launchpad:**
    
    - Go to your Nextflow workspace and open the launchpad.
2. **Add a New Pipeline:**
    
    - Click on `Add Pipeline`.
3. **Choose Compute Environments:**
    
    - Select `Compute Environments` you just created.
4. **Specify the GitHub Path:**
    
    - Enter the path to the Multifish pipeline on GitHub.

### 3. Configure Advanced Options

1. **Check Advanced Options:**
    
    - Navigate to the Advanced options section.
2. **Clear Advanced Options:**
    
    - Select all content within Advanced options and press `space` to remove any existing configurations.
3. **Enable "Pull Latest":**
    
    - Check the box for `Pull latest` to ensure you get any updates.

### 4. Configure Pipeline Parameters

1. **Download Example JSON File:**
    
    - Download the `example_slurm.json` file from GitHub, which contains necessary parameters.
2. **Edit JSON File:**
    
    - Open the `example_slurm.json` file and edit the parameters according to your requirements.
3. **Upload Edited JSON:**
    
    - Go to the parameters setting page in Nextflow and upload the edited JSON file.
4. **Customize Other Parameters:**
    
    - After uploading the JSON file, customize any additional parameters as needed.

## Run pipeline locally
TOWER_WORKSPACE_ID can be found [here](https://cloud.seqera.io/orgs/Wang_Lab/workspaces)
```
export TOWER_ACCESS_TOKEN=<YOUR TOKEN>

export TOWER_WORKSPACE_ID=000000000000000
```
Run your Nextflow workflows as usual with the addition of the -with-tower command (refer to https://github.com/Wang-BrainBody-Lab/EASI-FISH).

