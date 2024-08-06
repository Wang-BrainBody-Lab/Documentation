- [Nextflow Tower Agent](#nextflow-tower-agent)
  * [Installation​](#installation)
  * [Quickstart](#quickstart)
- [Run pipeline](#run-pipeline)
  * [1. Create Slurm Workload Manager](#1-create-slurm-workload-manager)
  * [2. Add Pipeline in Nextflow Workspace](#2-add-pipeline-in-nextflow-workspace)
  * [3. Configure Advanced Options](#3-configure-advanced-options)
  * [4. Configure Pipeline Parameters](#4-configure-pipeline-parameters)
 


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
    
3. (Optional) Move it into a folder that is in your path.

### [Quickstart](https://docs.seqera.io/platform/23.1.0/agent#quickstart "Direct link to Quickstart")

Click the "Quickstart" for more details.

Before running the Agent:

1. Create a [**personal access token**](https://docs.seqera.io/platform/23.1.0/api/overview#authentication) in Tower. Your personal authorization token can be found in the user top-right menu under [Your tokens](https://cloud.seqera.io/tokens).
2. Create **Tower Agent** credentials in a Tower workspace. See [here](https://docs.seqera.io/platform/23.1.0/credentials/overview) for more instructions.
3. When you create the credentials you'll get an **Agent Connection ID**. You can use the default ID or enter a custom ID — the connection ID in the workspace credentials must match the ID entered when you run the agent.

The agent should always be running in order to accept incoming requests from Tower. We recommend that you use a terminal multiplexer Screen, so that it keeps running even if you close your SSH session.

Input the following command in your terminal, replace with your real Token and ID.

```
export TOWER_ACCESS_TOKEN=<YOUR TOKEN>  
./tw-agent <YOUR CONNECTION ID> --work-dir= <YOUR WORK DIRECTORY>
```
To use screen, run the following `screen_job.sh` script (After creating this file, please use `chmod +x screen_job.sh` to make it executable.) use `sbatch screen_job.sh`instead, replace the Token and ID with true value. You can use `srun --jobid $SLURM_JOB_ID --pty screen -r slurm_job_$SLURM_JOB_ID` to attach the SCREEN sessions, and detach from it at any time with `Ctrl-A` followed by `D`.

- to see your screen sessions: `use screen -ls`
- to quit any screen session: `screen -S <session ID> -X quit`

```bash
#!/bin/bash
#SBATCH --job-name=screen_job
#SBATCH --output=screen_job_%j.out
#SBATCH --error=screen_job_%j.err
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --time=48:00:00
#SBATCH --mem=4G
#SBATCH --partition=regular

# Check if screen is available
if ! command -v screen &> /dev/null
then
    echo "screen could not be found. Please ensure it's installed on your system."
    exit 1
fi

# Create a unique name for this screen session
SCREEN_NAME="slurm_job_$SLURM_JOB_ID"

# Print information about how to connect to this screen session
echo "Screen session created: $SCREEN_NAME"
echo "To connect to this session from a login node, use:"
echo "srun --jobid $SLURM_JOB_ID --pty screen -r $SCREEN_NAME"

# Start a detached screen session
screen -wipe
screen -dmS $SCREEN_NAME

# Function to send commands to the screen session
send_command() {
    screen -S $SCREEN_NAME -X stuff "$1"
}

# Replace <YOUR TOKEN> and <YOUR CONNECTION ID> with actual values
TOWER_ACCESS_TOKEN="<YOUR TOKEN>"
CONNECTION_ID="<YOUR CONNECTION ID>"

# Send commands to the screen session
send_command "export TOWER_ACCESS_TOKEN=$TOWER_ACCESS_TOKEN\n"
send_command "./tw-agent $CONNECTION_ID\n --work-dir=./work"

# Keep the SLURM job running until the time limit is reached or the screen session ends
while screen -list | grep -q $SCREEN_NAME
do
    sleep 360
done

echo "Screen session ended. SLURM job complete."
```

The nextflow tower agent is online now!

## Run pipeline
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

