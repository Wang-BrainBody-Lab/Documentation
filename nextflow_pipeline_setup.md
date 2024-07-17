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

Before running the Agent:

1. Create a [**personal access token**](https://docs.seqera.io/platform/23.1.0/api/overview#authentication) in Tower. Your personal authorization token can be found in the user top-right menu under [Your tokens](https://cloud.seqera.io/tokens).
2. Create **Tower Agent** credentials in a Tower workspace. See [here](https://docs.seqera.io/platform/23.1.0/credentials/overview) for more instructions.
3. When you create the credentials you'll get an **Agent Connection ID**. You can use the default ID or enter a custom ID — the connection ID in the workspace credentials must match the ID entered when you run the agent.

The agent should always be running in order to accept incoming requests from Tower. We recommend that you use a terminal multiplexer Screen, so that it keeps running even if you close your SSH session.

Input the following command in your terminal, replace with your real Token and ID.

```
	export TOWER_ACCESS_TOKEN=<YOUR TOKEN>  
	./tw-agent <YOUR CONNECTION ID>
```
To use screen, Run the following shell script instead:

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

# Load any necessary modules
module load screen

# Start a detached screen session
screen -dmS my_screen_session

# Replace <YOUR TOKEN> and <YOUR CONNECTION ID> with actual values
TOWER_ACCESS_TOKEN="<YOUR TOKEN>"
CONNECTION_ID="<YOUR CONNECTION ID>"

screen -S my_screen_session -X stuff "export TOWER_ACCESS_TOKEN=$TOWER_ACCESS_TOKEN\n"
screen -S my_screen_session -X stuff "./tw-agent $CONNECTION_ID\n"

# Keep the screen session alive with an interactive bash shell
screen -S my_screen_session -X stuff "/bin/bash\n"

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
    
    - Download the `example_slurm.json` file from github, which contains necessary parameters.
2. **Edit JSON File:**
    
    - Open the `example_slurm.json` file and edit the parameters according to your requirements.
3. **Upload Edited JSON:**
    
    - Go to the parameters setting page in Nextflow and upload the edited JSON file.
4. **Customize Other Parameters:**
    
    - After uploading the JSON file, customize any additional parameters as needed.

